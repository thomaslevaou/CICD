# Déployez et testez votre code sur différents environnements

On apelle **tests d'acceptance** les tests permettant de vérifier que le code respecte toujours les exigences fonctionnelles.
On appelle **tests de performance** les tests vérifiant la performance du logiciel (toujours 100ms pour faire telle tâche, etc).

Les **smoke tests** sont les tests "de base" de l'application (j'ai un peu de mal à voir la différence avec les tests d'acceptance avec une définition pareille mais bon...). On peut en faire avec Selenium.

La méthode de livraison en **canary release** permet de ne réaliser une mise à jour que pour une petite partie des utilisateurs, de sorte que si elle se passe mal, alors seule une petite partie des utilisateurs est impactée par l'incident de prod.

La méthode de livraison en **Blue/Green** permet de rediriger temporairement l'infra vers la pré-prod / staging juste pour vérifier le bon fonctionnement de la MEP, puis de MEP quand tout est bon.

En vrai je vais arrêter le MOOC là. C'est trop superficiel pour être directement applicable, et l'application concrète dépend trop de chaque entreprise.