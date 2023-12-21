# Découvrez l'intégration continue

On appelle **intégration continue** (abrégé CI) l'ensemble de pratiques utilisées dans un logiciel consistant à vérifier qu'à chaque modification du code source, le résultat des modifications ne produit pas de régression.

La part technique d'une CI (hors planification de développement scrum) se réalise en 4 étapes (on parle de **chaîne d'intégration continue** ou **pipeline**):

1. **Compilation** et intégration du code (build), par exemple avec Maven en Spring;
2. **Tests** du code (unitaires principalement);
3. Mesure de la **qualité** du code. Sa différence avec les tests est que dans cette étape de qualité, on cherche à avoir la plus petite dette technique possible (je suppose le linter). Cette étape fait aussi parfois des tests de sécurité, mesure d'autres trucs que le développeur devra corriger ensuite, comme les vulnérabilités, la couverture de tests, les mauvaises pratiques (_code smells_), la complexité informatique, les duplications de code, etc. SonarQube est un exemple d'outil de mesure de qualité de code;
4. Gestion des **livrables** de l'application (package), qui doivent être déposés dans un dépôt de livrables et versionnés. On appelle **artéfacts** les binaires qui sont produits, qui peuvent être utiles pour des tests e2e, de performance, etc.

Les étapes d'une chaîne d'intégration continue doivent être automatisées par un **orchestrateur**, qui a pour rôle de reproduire ces étapes, et de proposer un tableau de bord qui permet aux utilisateurs d'en voir le suivi en temps réel. GitLab CI est un exemple d'orchestrateur.

Si le code n'atteint pas la qualité requise pour passer, on peut faire en sorte que la pipeline s'arrête, ne livre pas le code, et demande au développeur de corriger ses erreurs avant de reprendre sa livraison.

On va utiliser ici GitLab pour mettre en place une CI/CD.