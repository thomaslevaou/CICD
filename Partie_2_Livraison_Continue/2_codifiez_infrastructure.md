# Codifiez votre infrastructure

En soi, on peut utiliser Docker Compose en tant qu'outil d'IaC, d'une manière similaire à Terraform (et c'est là que je suis bien content d'avoir appris les deux...).
Docker Compose déploie l'application, mais aussi les tests post-déploiement évoqués dans le chapitre précédent (charge, perf, sécu, etc).

En production, on utilise généralement des plates-formes d'orchestration de conteneur comme Kubernetes (d'après le tuto, Docker Compose n'est pas assez solide en prod).

Les Dockerfiles sont déjà dispos dans le dossier `docker` de `string-petclinic-services`.

Si je modifie le numéro de version dans le Dockerfile, je peux pousser sans souci. Ce qui changera l'image créée sur le registry Gitlab.
Je peux alors récupérer le nom de l'image dessus, et le modifier dans le fichier `docker-compose.yml`. Ce qui permet à ce fichier d'IaC d'être lancé par les pipelines de livraison continue.
