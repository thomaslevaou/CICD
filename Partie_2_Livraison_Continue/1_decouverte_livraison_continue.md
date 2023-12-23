# Découvrez la livraison continue

Une fois un code déployé, on va pouvoir faire de nouveaux types de tests dessus, comme :

- des **tests de charge**, pour vérifier la capacité de l'application à gérer un volume important de requêtes ou transactions;
- des **tests de performance** permettent de vérifier la vitesse d'exécution et l'efficacité d'une application.
- des **smoke tests** permettent de vérifier que les fonctionnalités de base d'une application fonctionnent correctement.

Avec ces tests aussi, tout doit bien entendu être mesuré. **On ne peut pas améliorer ce qu'on ne sait pas mesurer**.

On appelle parfois **Site Relability Engineer** (SRE) la personne en charge d'améliorer la fiabilité des applications, en définissant et supervisant les métriques permettant cela. On va appeler ça le _monitoring_ ou la _supervision_ ici. Non seulement il va tester, mais il va chercher à automatiser tout ce qui peut être automatisé pour éviter le risque d'erreur humaine.

Le SRE se base généralement sur ce qu'on appelle les **quatre signaux dorés**, à savoir la latence, le trafic, les erreurs et la saturation, permettant de définir les **Service Level Indicators** (SLI), eux-mêmes permettant de définir les **Service Level Objectives** (SLO), qui sont à leur tour utilisés dans les **Service Level Agreements** (SLA).

Quelques exemples d'indicateurs permettant de mesurer la performance de corrections d'erreurs en prod:

- le **Mean-Time-Between-Failure** (MTBF) qui est le temps moyen séparant deux erreurs en production: plus le temps est élevé, plus le système est stable et fiable;
- le **Mean-Time-To-Recover** (MTTR) est le temps moyen de correction entre deux erreurs de correction : plus il est faible, plus l'équipe arrive à corriger rapidement.

New Relic est un outil assez pratique pour ce genre de métrique !

Et on peut aller encore plus loin dans l'étude de métriques de mesure, ce qui donne toute une discipline appelée dans ce contexte **l'observabilité**, qui est juste le nom donné à "on aimerait bien creuser plus en détail toutes les possibilités de New Relic ou autre outil de supervision, mais faut que quelqu'un puisse prendre le temps de le faire".

En entreprise, le rôle du DevOps ne s'arrête pas juste au fait que le code ait été déployé. Il doit aussi prendre en compte les différents retours des autres équipes une fois le code livré, et noter les retours dans le backlog.

Attention à ne pas confondre **déploiement continu** et **livraison continue**:

- On parle de livraison continue quand la mise en production reste un acte manuel dans le process de MEP;
- On parle de déploiement continu quand même l'acte de MEP est automatisé dans le pipeline.

Pour le moment dans ce cours, on va surtout se concentrer sur la livraison continue.

Dans une livraison continue, l'application est construite de manière à pouvoir être mise en production à n’importe quel moment.

Pour la mettre en place ici, on va ajouter des étapes à la pipeline:

- une codification de l'infra avec une **Infrastructure as Code**;
- un **déploiement et des tests** en environnement de test;
- un **maintien en condition opérationnelle** de l'app grâce au taff de Site Reliablity Engineer;
- une **supervision de l'application** comme évoquée ci-dessus avec les métriques du SRE généralement.