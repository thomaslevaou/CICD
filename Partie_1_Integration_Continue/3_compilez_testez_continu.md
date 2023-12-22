# Compilez et testez votre code en continu

On rappelle que les deux principaux workflows Git utilisés en entreprise sont le GitFlow (que je connais déjà) et le **trunk-based development**, où tout le monde pousse sur la branche `main` en gros. Je trouve ce deuxième flow un peu bizarre intuitivement, puisque ça veut dire que chaque code poussé sur main part en prod avant de partir en staging, mais je suppose que j'aurai une réponse à cette interrogation dans la partie sur le CD.

En tout cas dans le workflow GitFlow, l'intégration continue est surtout utile pour s'assurer que le code sur `develop` n'explose pas.

Sur GitLab, si notre compte se connecte à une 2FA, alors pour que je puisse accéder à un dépôt en fenêtre de commande, je dois créer un **Personal Access Token** (PAT) relié à l'API Git. Le PAT m'identifie sur GitLab : si quelqu'un y a accès en dehors de moi, il peut hacker mon compte sans trop de problème.

Notons que pour faciliter la pédagogie ici, on a cloné un projet GitLab vide (créé sur mon profil GitLab), et on a dedans ajouté un dépôt distant de GItHub. Et oui, c'est possible avec les commandes suivantes :

```bash
# "upstream" doit sûrement dire quelque chose comme "je veux ajouter un remote mais le terme origin est déjà pris" je pense
# Je ne creuse pas plus pour gagner du temps
git remote add upstream https://github.com/spring-petclinic/spring-petclinic-microservices.git
git pull upstream master

# Ici, on pousse sur main du projet GitLab (pas github)
# Je suppose que "upstream" permet de dire un truc comme "dépôt distant secondaire", ou un truc comme ça
git push origin main
```

Attention au moment de pousser mon code, je dois renseigner mon identifiant et mon PAT dans le champ "Password".
