# Compilez et testez votre code en continu

On rappelle que les deux principaux workflow Git utilisés en entreprise sont le GitFlow (que je connais déjà) et le **trunk-based development**, où tout le monde pousse sur main en gros. Je trouve ce deuxième flow un peu bizarre intuitivement, puisque ça veut dire que chaque code poussé sur main part en prod avant de partir en staging, mais je suppose que j'aurai une réponse à cette interrogation dans la partie sur le CD.

En tout cas dans le workflow GitFlow, l'intégration continue est surtout utile pour s'assurer que le code sur `develop` n'explose pas.


