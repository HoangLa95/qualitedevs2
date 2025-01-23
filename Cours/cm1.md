# Coder proprement ?
```{figure} ../images/wtfm.jpg
:alt: WTF per minute
:align: center

Credits: osnews.com
```

## Pourquoi coder proprement ?

### Le coût d'un mauvais code

```{figure} ../images/Knight_Capital_Group.png
:alt: Knight Capital Group
:width: 600px
:align: center

Knight Capital Group, une société de bourse qui a perdu plus de 450 millions de dollars à cause d'une boucle infinie[^KCG]  (Credits: wikipedia.com)
```
[^KCG]: [L'histoire de la faillite du Knight Capital Group](https://dougseven.com/2014/04/17/knightmare-a-devops-cautionary-tale/)

### Le faux dilemme du développeur

:::{danger} Erreur à ne pas faire
Coder salement pour respecter une deadline au détriment de la productivité dans le futur. Un code sale va toujours vous ralentir instantanément.
:::

:::{seealso} La **SEULE** manière correcte de faire les choses
:class: dropdown
Coder proprement !
:::

## Avant de commencer à coder

```{figure} ../images/git.png
:alt: Git, GitHub et GitLab
:align: center

Git, GitHub et GitLab (Credits: gonullu.pardus.org.tr)
```
:::{note} Git
**Version control system** qui garde un dépôt (*repository*) de notre projet et garde l'historique de notre travail.
:::

:::{note} Ce que l'on va utiliser
**GitLab** : une application web pour la gestion de projets et de ses participants.
:::

```{figure} ../images/why-git.png
:alt: Pourquoi utiliser Git ?
:align: center

Pourquoi utiliser Git ? (Credits: javatpoint.com)
```

:::{hint} Quelques raisons pour utiliser Git
- [Gestion des versions](https://fr.wikipedia.org/wiki/Gestion_de_versions).
- Gestion des développeurs.
- Gestion des *issues*.
- Documentation de la progression du projet.
- Construction, test, déploiement automatiques.
:::
    

:::{note} Git dans ce cours
:class: dropdown
Nous nous arrêtons à une initialisation aux commandes de bases de Git pour stocker du code personnel et nous découvrons un peu l'aspect collaboratif de Git.
:::

Commandes importantes à connaître[^plus] :
- `git clone <repo>` : Cloner une copie locale de votre dépôt distant.
- `git add <file>` : Demander de suivre l'historique d'un fichier. 
- `git commit -m <message>` : Créer un nouveau point dans l'historique de votre programme avec l'ensemble des modifications suivies.
- `git push` : Diffuser les changements du dépôt local sur le dépôt distant.
- `git pull` : Télécharger le dépôt distant

[^plus]: Plus de commandes dans le premier TP !

```{figure} ../images/GitWorkflow.jpeg
:alt: Git Workflow
:align: center

Git Workflow (Credits: bytebytego.com)
```

## Organisation du cours

- Langage utilisé : C++
- IDE : VSCodium
- 6 Cours Magistraux
- 7 Travaux Pratiques

:::{important} Objectifs du cours
1. Initiation à Git avec les commandes de bases.
2. Apprendre des principes de bases pour coder proprement. 
3. Savoir utiliser un debugger.
4. Découvrir les tests unitaires et le TDD (Test Driven Development).
:::

:::{attention} Notation
- 8 QCMs.
- Dépôt Git noté.
:::