# Les branches Git

## Introduction aux branches

:::{note} Qu'est-ce qu'une branche Git ?
:class: dropdown
Une branche Git est une ligne de développement indépendante dans un dépôt. Elle permet de travailler sur de nouvelles fonctionnalités ou correctifs de manière isolée, sans affecter la version stable du projet sur la branche principale.
:::

:::{note} Avantages des branches Git
:class: dropdown
1. **Isolation des modifications** : Les branches permettent d'expérimenter et de développer des fonctionnalités sans risque d'introduire des bugs dans le code principal.
2. **Collaboration facilitée** : Plusieurs développeurs peuvent travailler sur différentes branches en parallèle sans risque de conflits.
3. **Gestion des versions** : Les branches permettent de maintenir différentes versions d'un projet, par exemple, une version stable pour la production et une autre pour le développement.
4. **Revue de code et tests** : Les branches facilitent la revue de code et les tests, car les modifications peuvent être fusionnées dans la branche principale après validation.
5. **Historique clair** : Elles offrent un historique de développement clair, permettant de suivre l'évolution de chaque fonctionnalité ou correctif.
:::

:::{warning} Éviter de push sur la branche principale
À partir de maintenant, pour les projets en équipe, il faut travailler sur des nouvelles branches et fusionner les changements sur la branche principale.
:::

## Comment nommer une branche ?

Nous allons adopter les conventions suivantes pour nommer les branches :
1. Le nom d'une branche est toujours de la forme : `<prefix>/<name>`.
2. Les `<prefix>` possibles sont :
    - `feature/` : pour les nouvelles fonctionnalités.
    - `bugfix/` : pour les correctifs de bugs.
    - `experiment/` : pour des essais.
    - `improvement/` : pour des améliorations (qualité du code).
3. `<name>` est toujours en **kebab-case**. 

## Opérations sur les branches

### Créer une branche

Vous pouvez créer une nouvelle branche avec `git branch <prefix>/<name>` puis aller sur cette branche avec `git checkout <prefix>/<name>`.
Par exemple :
```{code} sh
git branch feature/assign-students-to-houses
git checkout feature/assign-students-to-houses
```
Ou directement :
```{code} sh
git checkout -b feature/assign-students-to-houses
```
pour créer et aller sur la branche en une seule commande.

### Lister les branches

Pour lister toutes les branches (locales et distantes):
```{code} sh
git branch -a
```
Vous pouvez voir les branches qui sont en cours de travail et celles qui sont déjà fusionnées avec les options `--no-merged` et `--merged` respectivement.
```{code} sh
git branch -a --no-merged
git branch -a --merged
```

### Supprimer une branche

:::{important} Supprimer les branches fusionnées
:class: dropdown
Il faut supprimer les branches qui sont déjà fusionnées dans la branche principale.
:::

Pour supprimer une branche qui est déjà fusionnée :
```{code} sh
git branch -d <prefix>/<name>
```

:::{warning} Supprimer les branches en cours de travail
:class: dropdown
Pour supprimer une branche en cours de travail mais que vous allez abandonner :
```{code} sh
git branch -D <prefix>/<name>
```
Souvent, ce sont des branches `experiment` que vous allez potentiellement abandonner. De façon générale, il faut éviter de supprimer les branches en cours de travail pour ne pas perdre les modifications.
:::

## Travailler en équipe avec des branches

### Éviter les conflits
- **Branches disjointes** : Les branches ne doivent pas modifier les mêmes fichiers.
- **Un(e) responsable par branche** : Chaque branche (dont `main`) doit avoir un seul responsable, mais les autres membres peuvent contribuer.
- `git merge main` : Quand une branche est fusionnée avec la branche principale, les autres branches en cours de travail doivent récupérer ces modifications en utilisant la commande `git merge main`. Cela permet de ne pas avoir à résoudre des conflits complexes lors des merge requests.

### Merge request

:::{danger} Interdiction de squash, rebase ou force push
Ces commandes peuvent altérer l'historique du dépôt, entraînant ainsi des conflits. Elles peuvent également modifier les auteurs des commits, affectant donc les contributions des membres.

Pour disable le squash sur GitLab :
1. Allez dans **Settings** > **Repository** > **Branch rules**.
2. Dans `All branches`, si vous voyez **Squash commits: Allow**, alors il faut cliquer sur **View details**.
3. Dans **Squash commits when merging**, cliquer sur **Edit**.
4. Choisissez l'option **Do not allow**.
:::

Merge request
: Demande de fusion d'une branche de travail vers la branche principale.

Le **responsable de la branche** de travail devrait faire le merge request.

:::{attention} Résoudre les conflits avant de soumettre le merge request
:class: dropdown
Le responsable de la branche de travail devrait résoudre les conflits avant de soumettre le merge request en faisant `git merge main` dans sa branche.
:::

Pour soumettre un merge request en utilisant l'interface GitLab.
1. Allez dans **Code** > **Merge requests**.
2. Cliquez sur **New merge request**.
3. Choisissez la branche de travail comme **source branch** et `main` comme **target branch**.
4. Cliquez sur **Compare branches and continue**.
5. Écrivez un titre et une description de la demande.
6. Choisissez **Assignees** : seul le responsable de la branche suffit.
7. Choisissez **Reviewers** : seul le responsable de la branche `main` suffit.
8. Dans **Merge options**, cochez **Delete source branch when merge request is accepted**.
9. Cliquez sur **Create merge request**.

:::{important} Assignee et Reviewer
:class: dropdown
Assigne est celui qui va résoudre les problèmes dans le code soumis (typiquement les conflits de fusions).
Reviewer est celui qui va examiner le code soumis et valider la fusion.
Le reviewer peut faire des commentaires sur le code que vous avez et demander des changements (à faire par assignee) avant de faire la fusion. 
:::

:::{seealso} Pull request
:class: dropdown
Un pull request est la même chose qu'un merge request.
Le terme pull request est utilisé par GitHub et le terme merge request est utilisé par GitLab.
:::

### Changer de branches en cours de travail

Si vous êtes en cours de travail et que vous avez besoin de changer de branches pour travailler sur d'autres fonctionnalités, la façon la plus sécurisée pour ne pas accidentellement perdre vos modifications est de **faire un commit de votre travail courant avant de changer de branche**.

Une autre possibilité est de garder ces modifications dans une pile de travail.
1. Par exemple, vous êtes en train de travailler sur `feature/assign-students-to-houses` et un autre membre du groupe vous demande de l'aide sur `bugfix/crash-on-startup`.
2. Vous n'êtes pas prêt pour faire un commit de `feature/assign-students-to-houses` parce que vous voulez garder un historique propre.
3. Vous pouvez utiliser la commande `git stash push -u -m "<message>"` pour mettre toutes les nouvelles modifications (`-u` inclus les nouveaux fichiers crées) depuis le dernier commit dans un élément d'une pile de travail gardé par Git avec un `<message>` explicatif.
4. Vous pouvez ensuite faire `git checkout bugfix/crash-on-startup` et travailler sur la branche `bugfix/crash-on-startup`.
5. Une fois que êtes revenu à `feature/assign-students-to-houses`, vous pouvez utiliser la commande `git stash pop` pour remettre vos modifications et enlever l'élément correspondant de la pile.

:::{seealso} `git stash list`
:class: dropdown
La commande `git stash list` permet de voir les éléments de la pile de travail.
Si vous avez plusieurs éléments, `git stash pop` récupère toujours l'élément le plus récent (`stash@{0}`).
Vous pouvez aussi choisir l'élément que vous voulez récupérer avec `git stash pop stash@{<numéro>}`.
:::

:::{important} Quiz Git
N'oubliez pas de faire le Quiz sur le Moodle de la SAÉ, dont la date limite est le dimanche 30/03/2025 à 23h59.
:::