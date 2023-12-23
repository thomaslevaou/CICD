# Compilez et testez votre code en continu

## Rappels de workflows Git

On rappelle que les deux principaux workflows Git utilisés en entreprise sont le GitFlow (que je connais déjà) et le **trunk-based development**, où tout le monde pousse sur la branche `main` en gros. Je trouve ce deuxième flow un peu bizarre intuitivement, puisque ça veut dire que chaque code poussé sur main part en prod avant de partir en staging, mais je suppose que j'aurai une réponse à cette interrogation dans la partie sur le CD.

En tout cas dans le workflow GitFlow, l'intégration continue est surtout utile pour s'assurer que le code sur `develop` n'explose pas.

## Paramétrages GitLab

Sur GitLab, si notre compte se connecte à une 2FA, alors pour que je puisse accéder à un dépôt en fenêtre de commande, je dois créer un **Personal Access Token** (PAT) relié à l'API Git. Le PAT m'identifie sur GitLab : si quelqu'un y a accès en dehors de moi, il peut hacker mon compte sans trop de problème.

Notons que pour faciliter la pédagogie ici, on a cloné un projet GitLab vide (créé sur mon profil GitLab), et on a dedans ajouté un dépôt distant de GitHub. Et oui, c'est possible avec les commandes suivantes :

```bash
# "upstream" doit sûrement dire quelque chose comme "je veux ajouter un remote mais le terme origin est déjà pris" je pense
# Je ne creuse pas plus pour gagner du temps
# Attention dans cette situation on doit bien prendre l'URL en HTTPS, pas l'URL en SSH
git remote add upstream https://github.com/spring-petclinic/spring-petclinic-microservices.git
git pull upstream master

# Ici, on pousse sur main du projet GitLab (pas github)
# Je suppose que "upstream" permet de dire un truc comme "dépôt distant secondaire", ou un truc comme ça
git push origin main
```

Attention au moment de pousser mon code, je dois renseigner mon identifiant et mon PAT dans le champ "Password".

## Mise en place de la pipeline

Sur [la page d'accueil du projet du dépôt GitLab que je viens de créer](https://gitlab.com/Peterkolios/spring-petclinic-microservices), je clique sur le bouton à droite `Set up CI/CD`, puis sur `Configure pipeline`. Ce dernier bouton crée un fichier `.gitlab-ci.yml`, dans lequel toute la syntaxe CI/CD doit être décrite avec la syntaxe YAML. C'est un fichier caché: il se voit sur VS Code, mais pas dans l'explorateur de fichiers standard de mon OS.

Dans ce fichier créé sur GitLab, on va remplacer le contenu mis par défaut, par le contenu suivant :

```YAML
stages:
 - build
 - test

cache:
 paths:
   - .m2/repository
 key: "$CI_JOB_NAME"

build_job:
  stage: build
  image: eclipse-temurin:17-jdk-alpine
  script:
    - ./mvnw compile
      -Dhttps.protocols=TLSv1.2
      -Dmaven.repo.local=$CI_PROJECT_DIR/.m2/repository
      -Dorg.slf4j.simpleLogger.log.org.apache.maven.cli.transfer.Slf4jMavenTransferListener=WARN
      -Dorg.slf4j.simpleLogger.showDateTime=true
      -Djava.awt.headless=true
      --batch-mode --errors --fail-at-end --show-version -DinstallAtEnd=true -DdeployAtEnd=true

test_job:
  stage: test
  image: eclipse-temurin:17-jdk-alpine
  script:
    - ./mvnw test
      -Dhttps.protocols=TLSv1.2
      -Dmaven.repo.local=$CI_PROJECT_DIR/.m2/repository
      -Dorg.slf4j.simpleLogger.log.org.apache.maven.cli.transfer.Slf4jMavenTransferListener=WARN
      -Dorg.slf4j.simpleLogger.showDateTime=true
      -Djava.awt.headless=true
      --batch-mode --errors --fail-at-end --show-version -DinstallAtEnd=true -DdeployAtEnd=true
```

Les blocs sont un peu différents de ceux que j'ai vus en entreprise, mais bon, ça doit dépendre au cas par cas en fonction du projet sur lequel je travaille.

Décrivons chacun des blocs :

- Le bloc `build_job` décrit la compilation du code, et le bloc `test_job` les tests unitaires;
- `stages` liste l'odre des étapes : le build a lieu avant les tests, et donc les **jobs**, c'est-à-dire les tâches de ces étapes, se dérouleront dans cet ordre;
- Le bloc `cache` permet en gros de garder en cache les dépendances et bibliothèques externes installée pour compiler et tester Java et Maven.

Chacun des blocs `jobs` contient trois sous-parties aux titres similaires :

- `stage`, comme son nom l'indique, donne le nom de l'étape concernée (oui il faut le préciser), et qui apparaîtra sur GitLab dans la pipeline de CI;
- `script` indique les commandes à lancer (le script) pour exécuter l'étape (ici de compilation ou de tests). Ici le script de build installe Maven et lance la compilation du projet (c'est ce qu'a surtout l'air de faire `./mvnw compile`, après le reste a surtout l'air des options pour faire tourner correctement le script);
- `image` correspond à l'image Docker (parce que oui, GitLab utilise Docker et en a besoin pour avoir un environnement où exécuter les scripts ici).

Si un seul test du script de `test` échoue, le pipeline s'arrête et envoie un e-mail au développeur concerné, pour lui dire que du caca est apparu. Pour qu'ils s'exécutent au plus vite, on recommande qu'ils n'aient aucune dépendance vis-à-vis de systèmes externes (bdd, système de fichiers du serveur, etc).

Une fois que le fichier `.gitlab-ci.yml` a été commité, on peut voir le déroulement des deux stages définis ci-dessus dans la pipeline, sur [cette page](https://gitlab.com/Peterkolios/spring-petclinic-microservices/-/pipelines), ou `Build > Pipelines` dans le menu de gauche.
En cliquant sur le "status" d'une pipeline, on voit plus de détails, notamment sur les jobs qui se lancent.
Quand une CI a échoué et qu'on clique sur les détails dans la pipeline pour voir les logs techniques, on a un bouton en haut à droite pour créer une issue à ce sujet si besoin.

Pour résoudre le problème, on peut passer par une Merge Request, et tout ça je connais déjà donc je ne reviens pas dessus.
Enfin c'est quand même important de savoir que si la description de la MR est de la forme `Closes #3` (ou numéro de ticket correspondant qui a été créé lors du fail de pipeline), le ticket associé est automatiquement fermé après MEP de la MR.
