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

## Dépôt local

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
touch my-first-file.txt
git status
```

:::{hint} Fichiers et modifications non suivis
:class: dropdown
Git indique que votre répertoire de travail contient un nouveau fichier ou une modification **non suivie** (*untracked*).

Git est un outil de gestion de versions : il vous permet de conserver l’historique des changements des fichiers de votre projet. Cependant, tous les fichiers ou modifications ne sont pas nécessairement importants. Ainsi, par défaut, les fichiers que vous créez ou les modifications que vous effectuez ne sont pas suivis. Seuls les fichiers et modifications explicitement suivis auront leur historique enregistré.
:::

3. Ajoutez une ligne dans `my-first-file.txt` (par exemple `Hello World!`).

4. Demandez à Git de suivre la modification à `my-first-file.txt` en exécutant la commande suivante.
```{code} sh
git add my-first-file.txt
```

5. Exécutez à nouveau la commande `git status`. Que vous indique-t-elle ?

:::{important} Modifications indexées  
Une fois qu'un fichier ou une modification est suivi, il change de statut et devient une modification indexée (*staged change*).
:::

:::{seealso} git restore --staged
:class: dropdown
Si vous avez ajouté par erreur une modification ou un fichier, vous pouvez retirer (*unstage*) la modification indexée en utilisant la commande `git restore --staged <nom du fichier>`.

Par exemple, exécutez `git restore --staged my-first-file.txt`, puis vérifier l'état du dépôt avec `git status`. N'oubliez-pas de resuivre la modification en utilisant `git add`.
:::

6. Maintenant, pour créer un historique des modifications indexées (ici, le fichier `my-first-file.txt` avec la ligne `Hello World!`), vous devez valider (*commit*) ces changements et ajouter un message décrivant ces modifications en exécutant la commande `git commit -m "<message de commit descriptif>"`.

Par exemple,
```{code} sh
git commit -m "Premier commit de my-first-file.txt"
```

7. Réexécutez `git status` pour vérifier l'état de votre dépôt. Que vous indique-t-elle ?

:::{hint} Ready to push
:class: dropdown
La validation a été effectuée. Votre version locale du projet est maintenant en avance par rapport à la version sur le serveur GitLab. Vous pouvez donc diffuser (*push*) ces changements pour synchroniser les deux versions.
:::

8. Exécutez `git push`.

9. Retournez sur Gitlab et vérifiez que le dépôt distant correspond bien au dépôt local.

## Dépôt distant

:::{important} Web IDE de GitLab
Avec le Web IDE de GitLab, il est possible de travailler directement sur le dépôt distant.

Les modifications que nous allons effectuer sur le dépôt distant simuleront les changements apportés par vous-même sur un autre poste de travail ou par un collaborateur de votre projet.
:::

1. Cliquez sur `my-first-file.txt`.
2. Cliquez sur **Edit**.
3. Sélectionnez l'option **Open in Web IDE**.
4. Ajoutez une deuxième ligne à ce fichier (par exemple, `Hi!`).

Vous pouvez voir un `1` sur la barre à gauche, indiquant qu'un changement a été effectué. 

```{image} ../images/pending-changes.png
:alt: Pending changes
:align: center
```

Si vous cliquez dessus, vous verrez un changement concernant le fichier `my-first-file.txt` et l'option pour sauvegarder sur la branche `main` (*Commit to 'main'*).

5. Ajoutez un message de commit.
6. Cliquez sur le bouton **Commit and push to 'main'**.
7. Cliquez sur **Continue** lorsque l'on vous demande si vous souhaitez sauvegarder sur la branche par défaut (main).

:::{seealso} Branches
:class: dropdown
La notion de **branches** en Git permet de maintenir une version stable d'une application ou d'un projet. Toutes les modifications sont donc effectuées sur des branches autres que 'main' (d'autres versions du dépôt), puis fusionnées avec cette branche plus tard.

Par exemple, dans un projet, la partie IHM et la partie logique du code peuvent être séparées en deux branches distinctes, autres que 'main'. La branche principale ('main') contiendra une version stable et fonctionnelle du projet, tandis que les deux autres branches contiendront des versions en développement.

Dans ce cours, nous ne couvrirons pas les branches Git, ainsi que d'autres fonctionnalités avancées de Git. Vous allez travaillez seulement sur une seule branche : 'main'.
:::

## Synchronisation du dépôt local avec le dépôt distant

Retournez sur votre dépôt local et supposons qu'un collaborateur a effectué des changements, puis les a diffusés sur le dépôt distant sans que vous en soyez informé.

1. Exécutez `git status`. Que remarquez-vous d'étrange ?

:::{important} `git status`
La commande `git status` ne va pas cherchez les changements sur le dépôt distant et n'affiche que les informations sur votre dépôt local.
:::

2. Exécutez `git remote update`, puis `git status`. Que voyez-vous maintenant ?

La branche 'main' sur le dépôt distant est différente de votre dépôt local. 

3. Exécutez `git diff origin/main` pour voir les différences entre les deux dépôts.

:::{important} Commencer par `git remote update && git status` !
:class: dropdown
En pratique, lorsque vous travaillez sur un projet, il est fréquent qu'entre deux sessions de travail, quelqu’un d’autre ait modifié une partie du code. Si vous commencez une session de travail sans vérifier l'état de vos dépôts, vous risquez de rencontrer des conflits à résoudre (par exemple, si vous modifiez les mêmes parties de code de manière différente). 

Bien que nous allons parler de la résolution de conflits dans ce cours, il est préférable de les éviter. C’est pourquoi il est essentiel de toujours commencer par `git remote update && git status` !

Vous pouvez également exécuter `git diff origin/main` pour voir précisément les modifications qui ont été effectuées.
:::

4. Pour synchroniser votre dépôt local avec le dépôt distant (qui, cette fois, est en avance), exécutez `git pull`.

5. Pour consulter l'historique des commits, exécutez `git log`.

## Ignorer des fichiers

:::{important} Pourquoi ignorer des fichiers ?
Lors du développement, certains fichiers doivent être systématiquement **ignorés** dans les sauvegardes (par exemple, les fichiers générés lors de la compilation), car ils encombrent inutilement le dépôt.

Par exemple, un moteur de jeu complexe comme Unity peut nécessiter jusqu’à 1 Go de fichiers pour exécuter certains modules, parfois inutilisés par votre jeu. Si ces fichiers ne sont pas ignorés, vous pourriez être contraint de télécharger ou de diffuser 1 Go à chaque synchronisation de vos dépôts, alors que les fichiers spécifiques à votre jeu ne représentent que quelques Mo.

Les fichiers de configurations peuvent également provoquer des problèmes de compatibilité, notamment lorsque vous synchronisez des fichiers de configuration spécifiques à certaines machines avec d'autres.
:::

1. Créez un fichier `hello-world.cpp` (avec `touch hello-world.cpp` par exemple).

2. Recopiez le code suivant.

```{code} cpp
#include <iostream>
using namespace std;
int main() {
    cout << "Hello World!" << endl;
    return 0;
}
```

3. Compilez le code avec `g++ -o <nom du fichier> <nom du fichier>.cpp`.

Par exemple,
```{code} sh
g++ -o hello-world hello-world.cpp
```

4. Exécutez l'exécutable `hello-world` avec la commande `./hello-world`.

Les exécutables font partie des fichiers que nous souhaitons ignorer. 

:::{important} `.gitignore`
La gestion des fichiers ignorés se fait via un fichier `.gitignore` situé à la racine de votre projet. Dans ce fichier, vous pouvez ajouter tous les noms de fichiers et de dossiers que vous souhaitez ignorer en permanence.

Un exemple de [`.gitignore`](https://github.com/github/gitignore/blob/main/Unity.gitignore) pour les projets Unity qui sont plus complexes.
:::

5. Créez le fichier `.gitignore`.

:::{seealso} Pourquoi `.gitignore` commence par un `.` ?
:class: dropdown
Les fichiers dont les noms commencent par `.` sont des fichiers cachés (qui ne s'affichent pas quand vous utilisez un gestionnaire de fichiers). Ce sont souvent des fichiers de configurations.

Pour afficher les fichiers cachés dans un gestionnaire de fichiers, vous pouvez souvent appuyer sur `Ctrl+H` (`H` comme 'Hidden').
:::

6. Ajoutez le nom de l'exécutable `hello-world` dans `.gitignore`.

7. Exécutez `git add`, `git commit -m <message>` et `git push` pour envoyer le fichier `.gitignore` sur le dépôt distant.

8. Exécutez `git status`. Qu'est-ce qui change par rapport à d'habitude ?

:::{hint} Où sont mes fichiers non suivis ?
:class: dropdown
Normalement, comme nous n'avons pas ajouté l'exécutable `hello-world` avec `git add`, `git status` nous indique qu'il y a un fichier non suivi, potentiellement important. Cependant, grâce au fichier `.gitignore`, Git reconnaît que nous avons choisi d'ignorer `hello-world` et ne suivra jamais les modifications apportées à ce fichier.
:::

## Pour finir...

Finissons ce TP avec une petite réorganisation : créer un dossier TP1 dans votre projet et bouge `my-first-file.txt` vers ce dossier. Vous allez maintenir ce projet pendant le reste du cours en créant des dossiers séparés pour chaque TP. Est-ce que vous êtes capable maintenant de faire `add`, `commit`, `push` vous-même ?

:::{hint} `git add .`
:class: dropdown
Vous pouvez faire `git add .` pour suivre tous les nouveaux changements que vous avez fait (quand vous avez créé/modifié plusieurs fichiers) **dans le dossier courant**.

:::{warning} Garder un log compréhensible !
Il est conseillé de faire des commits structurés, c'est-à-dire qu'il faut :
- Écrire des messages de commit clairs.
- Si vous faites des modifications différentes qui n'ont pas de rapport les uns avec les autres, alors il faut faire des `add` et `commit` séparés  (par groupe de modifications qui correspondent à une même "thème") au lieu de faire `git add .`. 
:::

:::{important} `.gitignore`
- On commence avec `*` pour tout ignorer. 
- L'expression `!*.*` indique qu'il ne faut pas ignorer les fichiers avec une extension (autrement dit, les fichiers sans extension comme les exécutables sont ignorés). 
- L'expression `!*/` indique qu'il ne faut ignorer les dossiers.
:::

:::{important} À NE PAS OUBLIER !
Il faut ajouter votre intervenant dans votre projet !
- Cliquer sur **Manage** à gauche puis **Members**[^manage_members].
- Cliquer sur **Invite members** en haut à droite.
- Ajouter votre intervenant avec le rôle **Maintainer** (qui lui donne presque autant de droit sur le projet que vous l'**Owner**).
- **Si l'intervenant n'est pas sur votre projet, il ne pourra pas le noter et donc vous aurez 0 !** 
:::

[^manage_members]: ![Manage Members](../images/manage-members.png)