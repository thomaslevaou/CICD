# Garantissez la qualité de votre code et packagez votre application

## Critères de mesure de qualité

On peut mesurer la qualité d'un code en fonction de 6 critères :

1. La **complexité cyclomatique**, qui est en gros le nombre de "chemins" que peut prendre le code (un `if` crée deux chemins par exemples, un ajout de boucles `for` augmente cette complexité cyclomatique, etc);
2. La **couverture de code**, qui doit parcourir le plus de code et de chemins possibles;
3. Le **nombre de code smells** (les mauvaises pratiques);
4. La **dette technique**, qu'on définit ici comme le _temps_ nécessaire pour corriger les morceaux de code de mauvaise qualité;
5. Les **métriques des fonctions**, qui sont différents éléments de mesures concernant les fonctions : le nombre de fonctions, leur longueur, leur profondeur d'appel (on considère que plus une fonction est longue, plus elle est susceptible de contenir un bug, idem si elle nécessite plus d'arguments, si elle est souvent appelées, etc);
6. Les **tests de sécurités**, dont les trois principaux types sont les suivants:

- Les **Static Application Security Testing** (SAST) permettent de tester le code contre les vulnérabilités connues de l'application, sans avoir besoin de la compiler;
- Les **Dynamic Application Security Testing** (DAST) sont là pour tester une application déployée (en fait, on les utilise surtout pendant la phase de déploiement). La [fondation OWASP](https://owasp.org/) propose plein de tests de détection de failles de sécurité pour faire des DAST sur une application déployée. Ces tests peuvent être exécutés sans même avoir accès au code source ou binaire de l'application (juste besoin de la page web d'entrée par exemple);
- Les **Interactive Application Security Testing** (IAST) permettent de tester les failles de sécurité de l'application depuis "l'intérieur" (en regardant le binaire de l'app et son code source). Là comme ça, la différence avec les SAST ne me paraît pas très claire, mais je reviendrai sûrement modifier ce texte ici plus tard une fois que ce sera plus clair.

Des outils comme **SonarQube** permettent de mesurer la qualité d'un code selon ces indicateurs automatiquement. SonarQube a notamment pour particularité d'indiquer précisément qu'est-ce qu'il faudrait corriger et où le corriger pour que la qualité du code s'améliore.

Une fois que la qualité du code est validée, les livrables se gèrent par conteneurisation.

## Modification du fichier gitlab-ci

Une fois que le fichier `.gitlab-ci.yml` a été créé sur GitLab, pour le modifier le plus simple me semble être de le modifier à la racine de mon repo local.

Pour ajouter une mesure de qualité, on ajoute un stage (une étape) `quality` dans ce fichier, pour lequel on décrit le job associé `code_quality_job` comme ci-dessous :

```YAML
stages:
  - build
  - test
  - quality

...

code_quality_job:
  stage: quality
  allow_failure: true
  image: docker:stable
  services:
    - docker:dind
  script:
    - mkdir codequality-results
    - docker run
      --env CODECLIMATE_CODE="$PWD"
      --volume "$PWD":/code
      --volume /var/run/docker.sock:/var/run/docker.sock
      --volume /tmp/cc:/tmp/cc
      codeclimate/codeclimate analyze -f html > ./codequality-results/index.html
  artifacts:
    paths:
      - codequality-results/
```

La ligne `allow_failure: true` est là pour dire qu'on autorise un échec dans cette étape de la pipeline. Le pipeline continue même en cas d'échec de cette étape, car elle n'est pas considérée dans ce projet comme "critique".

La ligne `services` permet ici d'exécuter Docker _dans_ l'image, pour qu'on puisse exécuter l'analyse de code. En gros on a besoin de préciser cette ligne pour exécuter des commandes propres à Docker dans le script, en l'occurence ici `docker run`.

Le `script` associé monte en gros le code dans une image Docker et lance l'analyse du programme via `codeclimate` (? admettons).
Tout le script est exécuté dans l'image `docker:stable`.

Grâce à la partie `artifacts`, le résultat de l'analyse du scan sera accessible dans le dossier `codequality-results`, stocké par GitLab.

Après avoir poussé le code, on voit bien qu'une nouvelle partie de la CI est visible dans la pipeline sur GitLab.

L'artifact est alors visible dans le détail du job sur GitLab, en cliquant sur "Browse" dans la partie qui parle de l'artéfact, à la fin de l'exécution de la CI. La page GitLab affiche alors un explorateur de fichier, dans lequel on peut cliquer dans le premier dossier, puis avoir les résultats de l'analyse de code dans le fichier `index.html`.

## Création du livrable (packaging) de l'application

GitLab vient avec une registry docker incluse (oui la sienne, pas Docker Hub). Les images Docker sont donc ici stockées chez GitLab.

Pour pouvoir en utiliser une, on commence par modifier à nouveau le fichier `gitlab-ci` pour ajouter cette étape et son job, càd concrètement les blocs YAML ci-dessous :

```YAML
stages:
    - build
    - test
    - quality
    - package

...

package_job:
  stage: package
  image: eclipse-temurin:17-jdk-alpine
  services:
    - docker:dind
  variables:
    DOCKER_HOST: tcp://docker:2375
  script:
    - apk add --no-cache docker-cli-compose
    - docker login -u gitlab-ci-token -p $CI_JOB_TOKEN $CI_REGISTRY
    - ./mvnw install -PbuildDocker -DskipTests=true -DpushImage
      -Dhttps.protocols=TLSv1.2
      -Dmaven.repo.local=$CI_PROJECT_DIR/.m2/repository
      -Dorg.slf4j.simpleLogger.log.org.apache.maven.cli.transfer.Slf4jMavenTransferListener=WARN
      -Dorg.slf4j.simpleLogger.showDateTime=true
      -Djava.awt.headless=true
      --batch-mode --errors --fail-at-end --show-version -DinstallAtEnd=true -DdeployAtEnd=true
    - docker compose build
    - docker compose push
```

Il faut bien comprendre que **c'est ce job qui a pour rôle de compiler et d'encapsuler le projet dans un conteneur**, avant de le pousser sur la registry GitLab.

Ici le script installe un client Docker en faisant `apk add --no-cache docker-cli-compose` pour lancer des commandes propres à docker (je suppose que le `docker:dind` dans les services ne suffit pas pour certaines commandes nécessaires ici). Il se connecte ensuite à la registry gitlab avec la ligne ̀`docker login -u gitlab-ci-token -p $CI_JOB_TOKEN $CI_REGISTRY`, avant de créer l'image Docker avec la ligne `./mvnw install` en gros.

Et c'est comme ça qu'on peut, grâce à Docker notamment, déployer le même code dans différents environnements, code compilé dans un package immuable.
Il existe une page de registry de GitLab où on peut voir toutes les images Docker qui ont été créées suite à la création de ce livrable.

Sur le Saas GitLab que j'utilise ici, il existe des outils d'analyse permettant de détecter les **Common Vulnerabilities and Exposures** d'une image donnée, ou des outils d'analyse de dépendances entre les différents composants de l'application, etc. Ce qui peut être utile pour informer des gens pendant le déploiement si besoin (à creuser si un jour on a plus besoin de ça dans la boîte).

## Autres outils de CI

- **Jenkins**, c'est grosso modo la même chose que GitLab CI, les interfaces étant juste un peu différentes. Sur un nouveau projet on recommandera plutôt GitLab CI dont l'installation et la maintenabilité avec le temps sont plus simples; mais sur un projet existant Jenkins ça existe, et c'est pas trop mal quand même;
- **TravisCI** et **CircleCI** sont d'autres outils de CI vendus en Saas : ils sont moins techniques à implémenter et maintenir, car ce sont les fournisseurs qui sont responsables de la gestion et maintenance de ces outils y compris avec le temps, mais ils sont payants;
- **GitHub Actions** assez récent (donc pas encore hyper populaire), fait grosso modo la même chose que GitLab CI, mais sur GitHub.
