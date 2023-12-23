# Codifiez votre infrastructure

En soi, on peut utiliser Docker Compose en tant qu'outil d'IaC, d'une manière similaire à Terraform (et c'est là que je suis bien content d'avoir appris les deux...).
Docker Compose déploie l'application, mais aussi les tests post-déploiement évoqués dans le chapitre précédent (charge, perf, sécu, etc).

En production, on utilise généralement des plates-formes d'orchestration de conteneur comme Kubernetes (d'après le tuto, Docker Compose n'est pas assez solide en prod).

Les Dockerfiles sont déjà dispos dans le dossier `docker` de `string-petclinic-services`.

Si je modifie le numéro de version dans le Dockerfile, je peux pousser sans souci. Ce qui changera l'image créée sur le registry Gitlab.

La liste de mon registry de conteneurs GitLab est censée être disponible ici : <https://gitlab.com/Peterkolios/spring-petclinic-microservices/container_registry>.

Je peux alors modifier dans le fichier `docker-compose.yml` les `springcommunity/` par des `registry.gitlab.com/Peterkolios/` ici. Ce qui devrait permettre à ce fichier d'IaC d'être lancé par les pipelines de livraison continue. Mais j'ai franchement du mal à comprendre si c'est GitLab qui est censé créer ces images tout seul, ou si je dois faire une action en avant qui n'est pas précisée.
