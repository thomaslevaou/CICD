# Garantissez la qualité de votre code et packagez votre application

## Critères de mesure de qualité

On peut mesurer la qualité d'un code en fonction de 6 critères :

1. La **complexité cyclomatique**, qui est en gros le nombre de "chemins" que peut prendre le code (un `if` crée deux chemins par exemples, un ajout de boucles `for` augmente cette complexité cyclomatique, etc);
2. La **couverture de code**, qui doit parcourir le plus de code et de chemins possibles;
3. Le **nombre de code smells** (les mauvaises pratiques);
4. La **dette technique**, qu'on définit ici comme le _temps_ nécessaire pour corriger les morceaux de code de mauvaise qualité;
5. Les **métriques des fonctions**, qui sont différents éléments de mesures concernant les fonctions : le nombre de fonctions, leur longueur, leur profondeur d'appel (on considère que plus une fonction est longue, plus elle est susceptible de contenir un bug, idem si elle nécessite plus d'arguments, si elle est souvent appelées, etc);
6. Les **tests de sécurités**, qu'on va décrire plus en détail plus bas dans ce fichier.

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

La ligne `services` permet ici d'exécuter Docker _dans_ l'image, pour qu'on puisse exécuter l'analyse de code (? un peu chelou mais bon).

Le `script` associé monte en gros le code dans une image Docker et lance l'analyse du programme via `codeclimate` (? admettons).
Tout le script est exécuté dans l'image `docker:stable`.

Grâce à la partie `artifacts`, le résultat de l'analyse du scan sera accessible dans le dossier `codequality-results`, stocké par GitLab.

Après avoir poussé le code, on voit bien qu'une nouvelle partie de la CI est visible dans la pipeline sur GitLab.

L'artifact est alors visible dans le détail du job sur GitLab, en cliquant sur "Browse" dans la partie qui parle de l'artéfact, à la fin de l'exécution de la CI. La page GitLab affiche alors un explorateur de fichier, dans lequel on peut cliquer dans le premier dossier, puis avoir les résultats de l'analyse de code dans le fichier `index.html`.
