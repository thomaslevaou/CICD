# Commandes de ma résolution du test techniques de la partie 1

## Récupération du code sur GitHub dans un projet sur mon compte GitLab

Le projet sur mon compte GitLab s'appelant `spring-petclinic`. Il est public, et je fais attention à ne pas l'initialiser avec un readme à sa création.

```bash
# Sur mon repo vide sur GitLab aussi, je fais attention à cloner en HTTPS
# Je n'ai pas mis de clé SSH car on teste une autre manière d'interagir avec un repo Git, pour une fois
git clone https://gitlab.com/Peterkolios/spring-petclinic.git
cd spring-petclinic/

git remote add upstream https://github.com/spring-projects/spring-petclinic.git
git pull upstream main
git push origin main
```

Notons qu'au lieu de passer par l'interface GitLab, s'il y a un fichier `.gitlab-ci.yml` déjà à la racine du projet, GitLab crée tout seul automatiquement la pipeline de son côté.


Pour plus tard :

```YAML

test_job:
  stage: test
  image: laurentgrangeau/oc-devops:latest
  script:
    - ./mvnw test
      -Dhttps.protocols=TLSv1.2
      -Dmaven.repo.local=$CI_PROJECT_DIR/.m2/repository
      -Dorg.slf4j.simpleLogger.log.org.apache.maven.cli.transfer.Slf4jMavenTransferListener=WARN
      -Dorg.slf4j.simpleLogger.showDateTime=true
      -Djava.awt.headless=true
      --batch-mode --errors --fail-at-end --show-version -DinstallAtEnd=true -DdeployAtEnd=true

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

package_job:
  stage: package
  image: laurentgrangeau/oc-devops:latest
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

Je ragequit l'exercice, les erreurs sont incompréhensibles si on ne connaît pas Maven et qu'on ne sait pas ce qu'il y a dans l'image Docker.