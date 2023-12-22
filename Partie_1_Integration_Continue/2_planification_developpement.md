# Planifiez votre développement

Pour bien comprendre une CI/CD, certaines notions de gestion de projet informatique doivent être définies et / ou rappelées :

- On appelle **Ops** les membres des équipes opérationnelles. D'où le fait qu'on appelle **DevOps** les développeurs qui font le lien entre les devs et les équipes opérationnelles. Et _DevSecOps_ les DevOps appuyant un peu plus sur la sécurité.
- On appelle **epics** un corpus de tâches globales, qui peuvent être divisées en tâches plus précises. On parle aussi de "description haut niveau de ce que le client veut"
- On rappelle que **GitLab** est une plate-forme de gestion de dépôts Git en Cloud concurrente à GitHub. GitLab d'une plus grande popularité pour les outils d'intégration continue, tandis que GitHub est plus populaire (pour des raisons historiques) pour les codes sans CI/CD.
- En plus de la 2FA via une appli sur Cloud, on peut mettre en place un **SSO** dans la version entreprise de GitLab. Avec une identification unique pour tous les services de la boîte, un employé est plus facile à "tracer" pour l'identifier partout, notamment dans ses commits, et garantir ainsi une meilleure sécurité de l'entreprise.
- On rappelle aussi qu'un **backlog**, c'est une des "catégories" des issues boards sur GitLab (Development, Focus, General, Product, etc etc.)
- On appelle **Shift Left de la sécurité** le fait de penser à la sécurité informatique d'un projet en amont du développement, pour minimiser le coût de son implémentation (ce qui est juste la prudence. Les gens trouvent vraiment des anglicismes pour désigner n'importe quel concept qui relève juste du bon sens quand on a un peu d'expérience...)
- Une **Evil User Story** est une User Story représentant le comportement d'un acteur malveillant qui souhaite nuire à l'application. Elle commence généralement par "Un attaquant ne devrait pas pouvoir..."
- On appelle **Abuser Story** une User story qui représente les envies d'un acteur malveillant (oui c'est proche de l'evil user story), comme par exemple "En tant que client authentifié, je vois ce qui ressemble à mon numéro de compte dans l’URL, alors je le remplace par un autre numéro pour voir ce qui se passe".
- On rappelle qu'une user story n'est prête que si elle a été bien écrite et contient tous les éléments nécessaires à son développement durant le sprint. On appelle le fait de définir la définition d'une user story prête, une **Definition of Ready** (oui on donne vraiment des anglicismes à tout et n'importe quoi...).
- Un **milestone** GitLab représente en gros un sprint.
- On appelle **burndown chart** la liste des issues du sprint en cours.
- On peut ajouter un traçage du temps passé sur tel ticket dans GitLab, en mettant dans le commentaire `/spend 8h` quand on a passé 8h dessus par exemplex. Sur la page encore expérimentale des [time tracking reports](https://gitlab.com/-/timelogs), on peut voir le temps passé pour chaque utilisateur sur les différents tickets.
