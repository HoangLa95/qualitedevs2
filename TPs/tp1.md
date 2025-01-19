# TP1 : Découvrir Git et GitLab

:::{note} Disclaimer
:class: dropdown
Le tutoriel est en français, mais de nombreux mots-clés sont en Anglais. Il est essentiel de vous familiariser avec le langage informatique ainsi qu'avec les documentations techniques, qui sont majoritairement rédigées en anglais. 
:::

Avant de commencer à coder, il est important de se familiariser avec les outils permettant d'organiser votre travail de programmation.

:::{seealso} Git
[Git](https://fr.wikipedia.org/wiki/Git) est un outil de [gestion des versions](https://fr.wikipedia.org/wiki/Gestion_de_versions) largement utilisé. Il est déjà installé sur les machines du département. Si vous travaillez sur votre propre machine personnelle, vous pouvez l'[installer gratuitement](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
:::

:::{seealso} GitLab
[GitLab](https://fr.wikipedia.org/wiki/GitLab) est une plateforme qui vous permet de stocker vos projets, de les synchroniser entre votre différentes machines (poste de l'IUT, ordinateur personnel) et de les partager avec vos collaborateurs.
:::

## Activation de votre compte GitLab

En tant qu'étudiant à l'IUT d'Orsay, vous disposez déjà d'un compte sur le [GitLab de l'IUT](https://git.iut-orsay.fr/).

```{image} ../images/git-iut-orsay.jpg
:alt: git.iut-orsay.fr
:align: center
```

Pour vous connecter, utilisez votre identifiant et votre mot de passe du département.

## Personal Access Token

Pour établir une connexion sécurisée entre votre machine et le serveur GitLab de l'IUT, nous utiliserons un **Personal Access Token** (PAT).

Pour créer un PAT :
1. Accédez à votre profil en cliquant sur votre avatar (en haut à gauche) puis **Edit profile**.

```{image} ../images/edit-profile.png
:alt: Edit profile
:align: center
```

2. Dans le menu de gauche, cliquez sur **Access Tokens**.

```{image} ../images/access-token-menu.png
:alt: Access Tokens
:align: center
```

3. Ajouter un nouveau token en cliquant sur **Add new token**.

```{image} ../images/add-new-token.png
:alt: Add new token
:align: center
```

4. Donnez un nom au token qui reflète le poste de travail utilisé (par exemple `IUTOrsay` si vous travaillez sur un poste à l'IUT).
5. Définissez une date d'expiration d'un an (la durée maximale autorisée) à partir de la date d'aujourd'hui.
6. Sélectionnez tous les [scopes](https://docs.gitlab.com/ee/user/profile/personal_access_tokens.html#personal-access-token-scopes).
7. Cliquez sur **Create personal access token**.

:::{important} SAUVEGARDER VOTRE PAT !!!
Une fois que vous quittez la page de création, le PAT ne pourra plus être affiché. Il est indispensable de le conserver, car vous en aurez besoin pour accéder à vos projets !
:::

:::{note} Un PAT par poste de travail
Vous devez créer un PAT distinct pour chaque poste de travail que vous utilisez. Il est possible de créer autant de PAT que nécessaire.
:::

## Création d'un dépôt distant

1. Créez votre premier projet/dépôt (*repository*) en cliquant sur le bouton `+` en haut à gauche.

```{image} ../images/create-new-project.png
:alt: Create new project
:align: center
```

2. Créez un projet vide avec le nom `qualite-dev-s2-<prenom>-<nom>` (par exemple `qualite-dev-s2-hoang-la`).

:::{warning} Attention !
Le projet que vous allez créer contiendra tous les TPs de ce cours. Vous ne devez pas créer un nouveau projet pour chaque TP.
:::

:::{important} `monprojet`
Dans la suite, nous appellerons ce projet `monprojet`. N'oubliez pas de le remplacer par le nom que vous avez choisi.
:::

3. Sélectionnez l'option de visibilité "Privé" pour votre projet et cochez l'option pour initialiser le projet avec un README.

:::{important} README.md
Avoir un README en [Markdown](https://fr.wikipedia.org/wiki/Markdown) dans un projet est une pratique standard. Ce fichier sert de description du projet.  
:::

4. Parcourez le README par défaut proposé par GitLab.

:::{important} Linux
Les instructions suivantes sont destinées à Linux. Vous êtes **fortement conseillé** d'utiliser Debian (une distribution open source Linux) à l'IUT.

Si vous n'êtes pas à l'aise avec les commandes terminal sous Linux, n'oubliez pas que vous pouvez toujours utiliser un gestionnaire de fichiers et votre souris.
:::

:::{seealso} macOS
:class: dropdown
Les commandes Unix sous Linux et macOS sont très similaires, mais il se peut que vous deviez parfois adapter certaines d'entre elles.
:::

:::{seealso} Windows
:class: dropdown
Vous pouvez installer l'émulateur [Git for Windows](https://gitforwindows.org/).
:::

## Configuration du votre poste de travail

Votre projet a été créé sur le serveur de l'IUT. Maintenant, pour travailler sur ce projet depuis votre poste de travail local, vous devez d'abord le configurer.

```{code} sh
git config --global user.name "<Prénom> <Nom>"
git config --global user.email "<prenom>.<nom>@universite-paris-saclay.fr"
```

Vous pouvez maintenant cloner votre dépôt distant pour le télécharger sur votre poste local :
1. Cliquez sur le bouton **Code** situé à droite.

```{image} ../images/code-button.png
:alt: Code
:align: center
```

2. Copiez l'URL sous l'option **Clone with HTTPS**.

```{image} ../images/clone-https.png
:alt: Clone with HTTPS
:align: center
```

3. Ouvrez un terminal dans votre répertoire de travail.
4. Saisissez la commande suivante, en collant l'adresse copiée et en rajoutant votre login ainsi que votre PAT aux emplacements appropriés.

```{code} sh
git clone https://<login>:<Personal Access Token>@git.iut-orsay.fr/<login>/monprojet.git
```

Par exemple :
```{code} sh
git clone https://hla:1234thisIsYourPAT5678@git.iut-orsay.fr/hla/monprojet.git
```

:::{important} Personal Access Token
Vous devez ajouter le token que vous avez sauvegardé précédemment ! 
- **Pas le nom que vous lui avez donné !**
- **Pas votre mot de passe !**
- **Pas le Feed Token !** (que vous pouvez également trouver sur la page Access Token)

Si vous n'avez pas sauvegardé votre PAT, supprimez (*Revoke*) celui que vous avez déjà créé et refaites la procédure pour en générer un nouveau.
:::

## Travailler sur le dépôt local

Un répertoire portant le même nom (*dépôt local*) a maintenant été téléchargé sur votre machine.

1. Exécutez les commandes suivantes.

```{code} sh
cd monprojet/
ls
git status
```

La commande `ls` vous permet d'afficher l'ensemble des fichiers dans le répertoire, tandis que `git status` montre l'état du dépôt. Pour l'instant, cette dernière commande devrait indiquer qu'il n'y a rien à valider et que votre copie de travail est propre (*nothing to commit, working tree clean*).

2. Que constatez-vous en exécutant les commandes suivantes ?

```{code} sh
touch toto.txt
git status
```

:::{hint} Fichiers et modifications non suivis
:class: dropdown
Git indique que votre répertoire de travail contient un nouveau fichier ou une modification **non suivie** (*untracked*).

Git est un outil de gestion de versions : il vous permet de conserver l’historique des changements des fichiers de votre projet. Cependant, tous les fichiers ou modifications ne sont pas nécessairement importants. Ainsi, par défaut, les fichiers que vous créez ou les modifications que vous effectuez ne sont pas suivis. Seuls les fichiers et modifications explicitement suivis auront leur historique enregistré.
:::

3. Ajoutez une ligne dans `toto.txt` (par exemple `Hello World!`).

4. Demandez à Git de suivre la modification à `toto.txt` en exécutant la commande suivante.
```{code} sh
git add toto.txt
```

5. Exécutez à nouveau la commande `git status`. Que vous indique-t-elle ?

:::{important} Modifications indexées  
Une fois qu'un fichier ou une modification est suivi, il change de statut et devient une modification indexée (*staged change*).
:::

:::{seealso} git restore --staged
:class: dropdown
Si vous avez ajouté par erreur une modification ou un fichier, vous pouvez retirer (*unstage*) la modification indexée en utilisant la commande `git restore --staged <nom du fichier>`.

Par exemple, exécutez `git restore --staged toto.txt`, puis vérifier l'état du dépôt avec `git status`. N'oubliez-pas de resuivre la modification en utilisant `git add`.
:::

6. Maintenant, pour créer un historique des modifications indexées (ici, le fichier `toto.txt` avec la ligne `Hello World!`), vous devez valider (*commit*) ces changements et ajouter un message décrivant ces modifications en exécutant la commande `git commit -m "<message descriptif>"`.

Par exemple,
```{code} sh
git commit -m "Premier commit de toto.txt"
```

7. Réexécutez `git status` pour vérifier l'état de votre dépôt. Que vous indique-t-elle ?

:::{hint} Ready to push
:class: dropdown
La validation a été effectuée. Votre version locale du projet est maintenant en avance par rapport à la version sur le serveur GitLab. Vous pouvez donc diffuser (*push*) ces changements pour synchroniser les deux versions.
:::

8. Exécutez la commande suivante.

```{code} sh
git push
```

9. Retournez sur Gitlab et vérifiez que le dépôt distant correspond bien au dépôt local.

<!-- ## Travailler sur le dépôt distant

Avec le web IDE de GitLab, il est possible de travailler directement sur le dépôt distant.

Les changements que nous allons faire sur le dépôt distant vont simuler les changements faits par vous-même sur un autre poste de travail ou par un collaborateur de votre projet :
- Cliquer sur `toto.txt`.
- Cliquer sur **Edit**.
- Choisir l'option **Open in Web IDE**.
- Ajouter une deuxième ligne à ce fichier (par exemple `Hi!`).

Vous pouvez voir sur la barre à gauche un `1` indiquant un changement a été effectué[^pending_changes]. Si vous cliquez dessus, vous voyez un changement concernant le fichier `toto.txt` et l'option de sauvegarder sur la branche `main` (*Commit to 'main'*).

- Ajouter un message de commit et cliquer sur le bouton **Commit to 'main'**.
- Cliquer sur **Continue** quand on vous demande si vous voulez sauvegarder sur la branche par défaut (main).
:::{note} Branches
:class: dropdown
La notion de **branches** en Git permet de toujours garder une version stable d'une application/un projet. Toutes modifications sont donc faites sur d'autres branches que 'main' puis fusionnées avec la branche 'main' plus tard. Dans ce cours, nous n'allons pas aborder les branches en Git comme beaucoup d'autres utilités de Git que nous allons survoler. Les modifications sont donc faites directement sur 'main'.
:::

[^pending_changes]: ![Pending Changes](../images/pending-changes.png) -->

## Synchronisation du dépôt local avec le dépôt distant

Remettez-vous sur votre dépôt local et oublions que nous avons fait des changements sur le dépôt distant.

:::{important} Commencer par `pull` !
:class: dropdown
En pratique, quand vous travailler dans un projet, très souvent entre deux sessions de travail, quelqu'un d'autre a modifié un bout de code dans votre projet. Si vous commencer votre session de travail sans d'abord synchroniser votre code, vous allez vous faire face à des conflits à résoudre (par exemple si vous aller modifier les mêmes bouts de code de façons différentes). Nous allons survoler la résolution de conflits dans ce cours mais par principe, il faut l'éviter et donc toujours commencer par `git pull` !
:::

Pour synchroniser votre dépôt local avec le dépôt distant qui est cette fois celui en avance, vous devez faire :

```{code} sh
git pull
```
Vous pouvez aussi voir l'historique des commits en faisant :

```{code} sh
git log
```
Cela vous permet d'être au courant de l'évolution de votre projet.

## Ignorer les fichiers inintéressants

Lors du développement, il y a beaucoup de fichiers que l'on souhaite **ignorer** en permanence dans les sauvegardes (par exemple, les fichiers générés lors de la compilation) car ils polluent inutilement le dépôt. Cela peut aussi être source de problèmes quand vous récupérez des fichiers de configurations de certaines machines et que vous essayer de les synchroniser avec d'autres (problèmes de compatibilité).

La gestion de ces fichiers est donc faite avec un fichier `.gitignore` qui est à la racine de votre projet. Dans ce fichier, vous pouvez ajouter tout ce que vous ne voulez pas suivre :
- Créer un fichier `tobeignored` (avec `touch` par exemple).
- Créer un fichier `.gitignore`.
- Ajouter le nom du fichier `tobeignored` dans `.gitignore` (avec un éditeur de texte comme `pluma` sur Debian à l'IUT par exemple).
- Suivre, sauvegarder, et diffuser `.gitignore` sur le dépôt distant.

Maintenant, vérifier `git status`. Qu'est-ce qui change d'habitude ?

:::{hint} Où sont mes fichiers non suivis ?
:class: dropdown
Normalement, vu que nous n'avons pas suivi `tobeignored`, `git status` nous prévient en disant qu'il y a un fichier potentiellement important qui n'est pas suivi. Par contre, grâce au fichier `.gitignore`, Git se rend compte que nous avons fait le choix d'ignorer `tobeignored` et ne suivra jamais ce fichier.
:::

Un exemple de [`.gitignore`](https://github.com/github/gitignore/blob/main/Unity.gitignore) pour des projets plus complexes.


## Pour finir...

Finissons ce TP avec une petite réorganisation : créer un dossier TP1 dans votre projet et bouge `toto.txt` vers ce dossier. Vous allez maintenir ce projet pendant le reste du cours en créant des dossiers séparés pour chaque TP. Est-ce que vous êtes capable maintenant de faire `add`, `commit`, `push` vous-même ?

:::{hint} `git add .`
:class: dropdown
Vous pouvez faire `git add .` pour suivre tous les nouveaux changements que vous avez fait (quand vous avez créé/modifié plusieurs fichiers) **dans le dossier courant**.

:::{warning} Garder un log compréhensible !
Il est conseillé de faire des commits structurés, c'est-à-dire qu'il faut :
- Écrire des messages de commit clairs.
- Si vous faites des modifications différentes qui n'ont pas de rapport les uns avec les autres, alors il faut faire des `add` et `commit` séparés  (par groupe de modifications qui correspondent à une même "thème") au lieu de faire `git add .`. 
:::
:::

:::{important} À NE PAS OUBLIER !
Il faut ajouter votre intervenant dans votre projet !
- Cliquer sur **Manage** à gauche puis **Members**[^manage_members].
- Cliquer sur **Invite members** en haut à droite.
- Ajouter votre intervenant avec le rôle **Maintainer** (qui lui donne presque autant de droit sur le projet que vous l'**Owner**).
- **Si l'intervenant n'est pas sur votre projet, il ne pourra pas le noter et donc vous aurez 0 !** 
:::

[^manage_members]: ![Manage Members](../images/manage-members.png)