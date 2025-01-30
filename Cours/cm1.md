# Développer proprement ?

## Qualité du code

### Le coût d'un code de mauvaise qualité

```{figure} ../images/knight-capital-group.png
:alt: Knight Capital Group
:width: 600px
:align: center

Knight Capital Group, une société de bourse qui a perdu plus de 440 millions de dollars à cause d'une boucle infinie.  (Credits: [Wikipedia](https://fr.wikipedia.org/wiki/Knight_Capital_Group))
```

### Le faux dilemme du développeur

:::{danger} Dilemme
Je dois écrire un code qui ne doit pas être maintenu ou réutilisé et la deadline arrive bientôt.

Est-ce que 
- [ ] je perds mon temps à coder proprement ?
- [X] je code n'importe comment tant que ça fait l'affaire ?
:::

:::{warning} Problèmes
:class: dropdown
- En tant que développeur, votre code doit souvent être maintenu et réutilisé.
- Avec de bonnes habitudes, la "perte de temps" est minimale pour un gain de temps maximal lors du développement.
- Même au cours d’une session de travail, un code sale peut entraîner d’énormes pertes de temps si vous devez le modifier.
:::

### Comment mesurer la qualité d'un code ?

```{figure} ../images/wtfm.jpg
:alt: WTF per minute
:align: center

Credits: [Osnews](https://www.osnews.com/story/19266/wtfsm/)
```

:::{important} Objectifs
:class: dropdown
- Lisible et cohérent.
- Facile à débugger.
- Facile à tester.
- Efficace par rapport à la demande.
- Évolutif si nécessaire.
:::

Plus dans les prochains amphis !

## Qualité du développement

### Développer seul et en équipe ?

En dehors de la qualité du code, quels sont les besoins de base du développement en solo et en équipe ?

:::{important} Développement en solo
:class: dropdown
- Un planning (cf. cours de gestion).
- Un environnement de développement convenable (variable selon le projet).
- **L'historique des versions du projet** (ce cours).
:::

:::{important} Développement en équipe
:class: dropdown
Besoins additionels :
- Une communication claire et régulière (cf. cours de gestion).
- Le partage du code sans conflit (initation dans ce cours).
:::

```{figure} ../images/centralized-workflow.png
:alt: Flux de travail centralisé
:align: center

Un flux de travail centralisé qui garde un dépôt partagé (*shared repository*) commun pour le développement en équipe
Credits: [Git](https://git-scm.com/book/en/v2/Distributed-Git-Distributed-Workflows)
```

### Git

```{figure} ../images/git.jpeg
:alt: Git, GitHub et GitLab
:align: center

Git, Bitbucket, GitHub et GitLab (Credits: [Linkedin/PawanV](https://www.linkedin.com/pulse/demystifying-git-vs-github-atlassian-bitbucket-gitlab-pawan-verma))
```

:::{note} Git
Git est un logiciel de [**gestion de versions**](https://fr.wikipedia.org/wiki/Gestion_de_versions) qui garde un dépôt (*repository*) de notre projet et l'historique des différentes versions de notre travail.
:::

:::{note} Bitbucket, GitHub et GitLab
Des applications web pour héberger des dépôts et faciliter le développement en équipe.
:::

:::{note} Dans ce cours
Nous nous limitons à une initialisation aux commandes de bases de Git pour stocker du code personnel et découvrons brièvement l'aspect collaboratif de GitLab.
:::

:::{important} Principe de Git et une platforme web comme GitLab
- **Répertoire de travail** (*Working directory*) : endroit où nous modifions nos fichiers.
- **Zone de suivi** (*Staging area*) : endroit temporaire pour les modifications suivies (*Staged changes*).
- **Dépôt local** (*Local repository*) : endroit contenant le code avec les modifications validées (*Committed changes*).
- **Dépôt distant** (*Remote repository*) : serveur distant contenant le code.

Les différentes zones sont présentes pour gérer le versionnage !
:::

:::{tip} Quelques commandes de bases à connaître
- `git add` permet de suivre une modification (faire passer une modification du répertoire de travail à la zone de suivi).
- `git commit` permet de valider les modifications suivies en ajoutant un message descriptif qui sera enregistré dans l'historique du projet (faire passer les modifications suivies de la zone de suivi au dépôt local).
- `git push` permet de diffuser les modifications validées du dépôt local au dépôt distant.
- `git pull` permet de récupérer les modifications du dépôt distant sur le répertoire de travail local.

```{figure} ../images/git-workflow.jpeg
:alt: Comment fonctionne Git et GitLab ?
:align: center

Comment fonctionne Git et GitLab ? (Credits: [Bytebytego](https://x.com/bytebytego/status/1648197336861130753?mx=2))
```

Plus de commandes dans les TPs !
:::

## Organisation du cours

- Langage utilisé : C++
- IDE : VSCodium
- 6 Cours Magistraux
- 7 Travaux Pratiques

:::{important} Objectifs du cours
- Savoir utiliser les commandes de bases de Git, un outil de gestion de versions.
- Connaître les principes de base du code propre.
- Savoir utiliser un debugger.
- Découvrir les tests unitaires et le TDD (Test Driven Development).
:::

:::{attention} Notation
- 8 QCMs.
- Dépôt Git noté.
:::