# Notation pour le projet

:::{attention} Commits individuels
Commit votre propre code, pas celui d'autres membres du groupe.
:::

:::{danger} Interdiction de réécrire l'historique
Interdiction d'utilisation de commandes git pour réécrire l'historique du projet partagé (pas de squash merge, rebase, ou force push).

Toute tentative de réécriture de l'historique du projet pour artificiellement gonfler votre contribution sera sanctionnée.
**Il faut me (M. La) notifier si l'historique est réécrit.**
:::

**La qualité du projet est notée sur 25 %.**

## Note collective 

:::{warning} Note pour les contributeurs
Si votre apport au projet est jugé insuffisant, votre note sera pondérée en conséquence (multipliée par un coefficient inférieur à 1).
:::

### Qualité du développement

**La qualité du développement est notée sur 4 %.**

1. Initialisation du dépôt Git
    - Le chemin vers le dépôt est correctement nommé.
    - Tous les membres (et les professeurs) sont ajoutés avec les rôles appropriés.
    - Le fichier README inclut les noms des membres et les informations essentielles sur le projet.
    - Le projet est bien organisé.
    - Le fichier .gitignore est présent et le projet est propre.

2. Gestion du dépôt Git
    - Utilisation efficace de .gitignore pour éviter la pollution du dépôt.
    - Structure du projet organisée et compréhensible.
    - Commits fréquents et bien ciblés, accompagnés de messages explicites.
    - Nommage et utilisation appropriés des branches.
    - Descriptions détaillées et revues approfondies des merge requests.
    - Utilisation claire et efficace des issues.

### Qualité du Code

**La qualité du code est notée sur 6 %.**

1. Conventions de nommage : utilisation correcte de camelCase, UpperCamelCase, UPPER_SNAKE_CASE et du shadowing.
2. Refactorisation selon le principe de responsabilité unique.
3. Documentation des méthodes publiques et commentaires pour les détails d'implémentation.
4. Validation des entrées et utilisation correcte des exceptions.

## Note individuelle

**La note individuelle est sur 15 %.**

1. Quiz sur la préparation au projet : **2 %**
2. Quiz sur les branches Git et les merge requests : **2 %**
3. Quiz final sur Git (en TP de Qualité) : **2 %**
4. Tests unitaires : **7 %**
    - Pourcentage de couverture des tests (pour ce qui vous est attribué) : comportement attendu, cas limites, cas exceptionnels.
    - Les tests sont bien nommés, corrects et fonctionnels.
    - Les tests respectent bien les principes FIRST.
    - Vous avez bien modifié vos tests suite aux retours sur les tests que vous avez reçus.
    - Vous avez bien modifié vos tests pour s'adapter aux évolutions du projet (réorganisation du code, renommage des fonctions).
5. Auto-évaluation : **2 %**
    - Détaillez votre contribution au code.
    - Les tests que vous avez écrits sont-ils corrects ?
    - Pour les fonctions que vous avez implémentées, passent-elles tous les tests ?
    - Avez-vous respecté tous les critères énumérés ci-dessus dans votre contribution ?
    - Si ce n'est pas le cas, expliquez les points non respectés et pourquoi.

## Récapitulatif

:::{important} Barème de notation
- Qualité du Développement : 4 %
- Qualité du Code : 6 %
- Quiz préparation : 2 %
- Quiz branches Git : 2 %
- Quiz SAÉ : 2 %
- Tests unitaires : 7 %
- Auto-évaluation : 2 %
**Total : 25 %**
:::