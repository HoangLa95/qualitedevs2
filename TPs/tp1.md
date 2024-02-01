# TP1 : Découvrir Git et Gitlab

Avant de commencer à coder, il faut se familiariser avec les outils d'organisation de votre travail de programmation.

## Introduction à Git et GitLab

[Git](https://fr.wikipedia.org/wiki/Git) est un outil de [gestion des versions](https://fr.wikipedia.org/wiki/Gestion_de_versions). Il est déjà installé sur les machines du département. Si vous travaillez sur votre machine personnelle, vous pouvez l'[installer vous-même](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) gratuitement.


[GitLab](https://fr.wikipedia.org/wiki/GitLab) est une plateforme qui permet de stocker vos projets et de diffuser ce travail à tous vos postes (machine de l'IUT, machine personnelle) ainsi qu'à vos collaborateurs.

### Activation de votre compte GitLab

En tant qu'étudiant de l'IUT d'Orsay, vous avez déjà un compte sur le [GitLab de l'IUT](https://git.iut-orsay.fr/).

```{image} ../images/git-iut-orsay.jpg
:alt: git.iut-orsay.fr
:align: center
```

Pour vous authentifier, utilisez vos login et mot de passe du département.

### Personal Access Token

Pour établir une connection sécurisée entre votre machine et le serveur GitLab de l'IUT, nous allons utiliser un **Personal Access Token** (PAT).

Pour créer un PAT :
1. Aller dans votre profile en cliquant sur votre avatar puis **Edit profile**[^edit_profile].
2. 

[^edit_profile]: ![Edit Profile](../images/edit-profile.png)

### Créer votre premier projet

Vous pouvez maintenant créer votre premier projet (avec le nom _MonProjet_):

<img src="ressources/new_project.png" width="75%" style="margin:auto;display:block;"/>

<!--![](/ressources/new_project.png/)-->

Votre projet (appelé également _dépôt_ ou _repository_) est créé sur le serveur GitLab de l'IUT.
Maintenant vous tombez sur la page par défaut d'un projet vide :

<img src="ressources/new_project_page.png" width="75%" style="margin:auto;display:block;"/> 

<!--![](ressources/warning_key.png)-->

Sur cette page, GitLab vous donne systématiquement le récapitulatif de quelques commandes Git à exécuter. Nous allons
les découvrir ensemble dans les sections suivantes.

**Remarque :** Le mini-tutoriel qui suit est fait pour Linux. Il s'adapte facilement sur Mac OS.
Pour les utilisateurs de Windows il faudrait adapter les commandes Unix à celles
de Windows ou installer un émulateur comme [Git for Windows](https://gitforwindows.org/)
qui permet de simuler l'utilisation de Git comme si vous étiez sous Linux.

#### Prise en main de Git

Tout d'abord rappelez-vous que GitLab est juste un **serveur d'hébergement** et Git est le **gestionnaire de versions** qui est utilisé au cœur de GitLab.
Vous utilisez Git localement sur votre machine. Et vous pouvez à tout moment décider de diffuser votre programme à travers le serveur d'hébergement.
La première chose à faire c'est de télécharger (ou cloner) une **copie** locale de votre projet depuis GitLab sur votre machine et d'établir une connexion authentifiée entre cette copie locale et la copie sur le serveur GitLab.
Pour cela :
```sh
cd votre_répertoire_de_travail
git clone git@gitlabinfo.iutmontp.univ-montp2.fr:VotreLogin/MonProjet.git
```

La commande `git clone` vous demandera le mot de passe que vous avez donné auparavant à `ssh-keygen`.
Après validation de ce mot de passe, un répertoire portant le même nom (`MonProjet`) sera téléchargé sur votre machine.
Ce répertoire est le _dépôt Git local_. La copie qui est sur GitLab est appelée _dépôt distant_. Nous allons voir ce que dépôt local contient.

```sh
 $ cd MonProjet
 $ ls
 $ git status
```
Observez la différence entre la commande `ls` et `git status`. La première est une commande Linux pour lister l'ensemble de fichiers dans votre répertoire, alors que la seconde montre l'état de "sauvegarde" de ces fichiers. Pour le moment, `git status` devra vous afficher le message "_rien à valider, la copie de travail est propre_" car vous n'avez pas encore effectué de modification de votre projet en local. Tapez les commandes suivantes pour voir la différence :

```sh
 $ touch toto.txt
 $ git status
```
Un message comme ceci devrait apparaître (ce sera en français si votre environnement est en français) :

<img src="ressources/PremierGitStatus.png" width="75%"  style="margin:auto;display:block;"/>

Il indique que votre répertoire de travail contient un fichier **non versionné** (la couleur rouge et le terme "not staged" l'indiquent). C'est normal, vous venez juste de créer ce fichier. Avant d'apprendre à sauvegarder (**versionner**) les modifications, vous allez configurer localement vos paramètres Git.

#### Configuration locale de Git

Maintenant que vous avez fait le tutoriel, vous pouvez correctement configurer **localement** Git sur votre machine.

Ouvrez le fichier `~/.gitconfig` avec votre éditeur favori (créez le fichier si nécessaire). Renseignez votre nom, prénom et email dans la section `[user]`.
```
# Personnalisez les champs ci-dessous!
[user]
username = prenom-nom
name = Prenom Nom
email = prenom.nom@etu.umontpellier.fr
```

L'expérience montre que les commandes suivantes (à taper dans un terminal) peuvent aussi être utiles :

```sh
# Si vous faites git commit sans donner le message avec -m,
# Git ouvrira l'éditeur gedit pour que vous tapiez le message
git config --global core.editor "gedit --new-window -w"
# Pour anticiper une erreur due aux certificats de l'IUT
#              "server certificate verification failed"
git config --global http.sslverify false
```

Après les avoir tapé, vous pouvez aller voir les modifications introduites dans le fichier `~/.gitconfig`.


### Un petit Salut le Monde qui va bien !
Faisons quelques modifications sur le fichier `toto.txt`que vous avez créé. Ajoutez-y une ligne : "Hello World !"
Souvenez-vous, que pour le moment Git ignore ce fichier car vous ne lui avez pas demandé de _suivre son historique_.

D'abord ajoutez ce fichier à l'index de suivi de votre historique :

```sh
~/MonProjet$ git add toto.txt
```
Cette commande indique à Git que vous voudriez suivre les modifications du fichier `toto.txt`, mais que la sauvegarde aura lieu plus tard (entre temps vous pouvez faire d'autres `git add`) !
En tapant `git status` vous devriez obtenir quelque chose comme ceci :

<img src="ressources/PremierAdd.png" width="75%" style="margin:auto;display:block;"/>

Maintenant on va sauvegarder les changements de votre projet que vous avez indexés (avec plusieurs `git add` auparavant) :

```sh
~/MonProjet$ git commit -m "Création d'un fichier important pour mon travail"
```

 En tapant `git status` vous devriez obtenir quelque chose comme ceci :

<img src="ressources/PremierCommit.png" width="75%" style="margin:auto;display:block;"/>

Le message indiqué montre que tout a été sauvegardé correctement. Et que votre _version locale_ de projet est en avance par rapport à la version sur le serveur d'hébergement GitLab. Vous pouvez maintenant diffuser ces changements locaux sur le dépôt distant :

```sh
~/MonProjet$ git push -u origin master
```

**Remarque** : ̀`git push` est un raccourci de la commande `git push origin master`. Le mot-clé `origin` indique le dépôt distant (celui sur le serveur GitLab).
Pour le moment vous n'avez pas besoin de connaître le sens du mot-clé `master`, donc vous pouvez ignorer sa signification pour l'instant et attendre la fin de ce tutoriel.

Retournez sur Gitlab et voyez que votre projet contient maintenant `toto.txt`.

## À faire après chaque réalisation importante

Si `fic1` et `fic2` sont deux fichiers dont vous voulez sauvegarder les changements :

```sh
~/MonProjet$ git add fic1 fic2
~/MonProjet$ git commit -m "ça y est, je deviens un boss de Git"
~/MonProjet$ git push
```

Un petit résumé des commandes :
 * `git add` prépare la future sauvegarde en suivant l'ensemble de modifications locales
 * `git commit -m "message de sauvegarde"` crée un nouveau point d'historique de votre programme en y enregistrant l'ensemble de modifications suivies. Le message obligatoire doit décrire cette sauvegarde
 * `git push` diffuse l'état local (après le dernier commit) de votre projet sur le dépôt distant

## Récupération du travail depuis le dépôt distant
Reclonons une copie de votre projet dans un autre répertoire et vérifions que l'on a bien récupéré le tout :

```sh
~/MonProjet$ cd
~$ mkdir AutreRepertoire
~$ cd AutreRepertoire
~/AutreRepertoire$ git clone git@gitlabinfo.iutmontp.univ-montp2.fr:VotreLogin/MonProjet.git
~/AutreRepertoire$ cd MonProjet
~/AutreRepertoire/MonProjet$ ls
~/AutreRepertoire/MonProjet$ git status
```

Modifiez le fichier `toto.txt` en donnant une réponse au message de salut.

```sh
~/AutreRepertoire/MonProjet$ git add toto.txt
~/AutreRepertoire/MonProjet$ git commit -m "réponse au bonjour"
~/AutreRepertoire/MonProjet$ git push
```

Votre dépôt distant est à jour mais la première copie locale du projet sur laquelle vous avez travaillé initialement est **en retard** dans l'historique. Donc il faut récupérer tous les nouveaux changements depuis le dépôt distant :

```sh
~/AutreRepertoire/MonProjet$ cd ~/MonProjet
~/MonProjet$ git pull
```
Vous devriez obtenir quelque chose comme ceci :


<img src="ressources/PremierGitPull.png" width="75%" style="margin:auto;display:block;"/>

### Affichage du journal des enregistrements (`commit`)

Une dernière commande utile est `git log` : elle affiche l'ensemble des enregistrements effectués (`commit`) avec leur message. Vous pouvez aussi retrouver toutes ces informations dans Gitlab dans l'onglet latéral Repository > Commit.

```sh
~/MonProjet$ git log
commit b8ae03f953e91567427dae65b09ac758d03b3316 (HEAD -> master, origin/master)
Author: Romain Lebreton <romain.lebreton@lirmm.fr>
Date:   Fri Oct 4 11:11:03 2019 +0200

    réponse au bonjour

commit 6d5139ad99201cd36cec350695c8671a98d2a00f
Author: Romain Lebreton <romain.lebreton@lirmm.fr>
Date:   Fri Oct 4 11:08:16 2019 +0200

    ça y est, je deviens un boss de Git

commit 1be15bc5a3d3a507016620b6044229d76b027a80
Author: Romain Lebreton <romain.lebreton@lirmm.fr>
Date:   Fri Oct 4 11:04:05 2019 +0200

    Création d'un fichier important pour mon travail
```

Vous l'aurez compris, lorsque vous avez plusieurs dépôts locaux, sur des machines potentiellement différentes, vous pouvez très bien travailler sur une de ces copies locales, sauvegarder vos changements (`git add` + `git commit`), pousser le tout sur le dépôt distant (`git push`) et récupérer le tout (`git pull`) dans les autres dépôts locaux.

## Résolution des conflits
Mais que se passe-t-il lorsque vous travaillez en parallèle sur deux dépôts locaux et que vous oubliez de les synchroniser correctement ? Simulons une telle situation :

 * faites une modification du fichier `toto.txt` en y ajoutant ou supprimant du texte dans le premier dépôt local
 * ensuite faites une sauvegarde des changements et diffusez sur le dépôt distant :
	```sh
	~/MonProjet$ git add .
	~/MonProjet$ git commit -m "sauvegarde dans la 1ere copie"
	~/MonProjet$ git push
	```
 * maintenant, faites une modification différente du fichier `toto.txt` en y ajoutant ou supprimant du texte dans le deuxième dépôt local
 * faites une sauvegarde des changements :
	```sh
	~/AutreRepertoire/MonProjet$ git add .
	~/AutreRepertoire/MonProjet$ git commit -m "sauvegarde dans la 2eme copie"
	```
 * si vous essayez de pousser ce changement vous aurez un message indiquant un **conflit** car les nouveaux changements sur le dépôt distant n'ont pas été récupérés dans la deuxième copie locale; Git ne fera donc rien et attendra vos indications pour résoudre le problème
	```sh
	~/AutreRepertoire/MonProjet$ git push
	To gitlabinfo.iutmontp.univ-montp2.fr:lebreton/MonProjet.git
    ! [rejected]        master -> master (fetch first)
    error: impossible de pousser des références vers
	       'git@gitlabinfo.iutmontp.univ-montp2.fr:lebreton/MonProjet.git'
	...
	```


Ce mécanisme de signalement de conflit est très pratique car vous n'êtes jamais à l'abri d'un oubli de synchroniser les dépôts. Une situation similaire se produit si vous avez travaillé à la maison sur un document et oubliez d'enregistrer les changements sur votre clé USB. En arrivant à l'IUT vous continuerez à travailler sur une version trop ancienne de votre document. Sauf que personne ne vous le dira et vous risquez de perdre du temps précieux avant de vous en rendre compte...

Git vous suggère d'intégrer d'abord les changements distants (avec 'git pull') avant de pousser à nouveau.
```sh
~/AutreRepertoire/MonProjet$ git pull
...
Fusion automatique de toto.txt
CONFLIT (contenu) : Conflit de fusion dans toto.txt
La fusion automatique a échoué ; réglez les conflits et validez le résultat.
```

Pour résoudre les conflits, Git propose plusieurs solutions :

 * si une fusion peut avoir lieu facilement (les changements qui sont en conflit ne portent pas sur les mêmes fichiers), il vous propose de fusionner le tout. Ici cela a échoué, cf le message précédent de `git pull`.
 * si le conflit porte sur le même fichier et vous voulez fusionner l'ensemble des changements, alors il faudrait l'indiquer explicitement à Git :
     ```sh
     ~/AutreRepertoire/MonProjet$ git merge origin/main
     ```
   Avec la commande ci-dessus, Git va essayer d'intégrer l'ensemble des changements des deux historiques en un seul. Et plusieurs cas de figures sont à envisager :

   1. Lorsque les deux historiques sont "compatibles", à savoir les changements du fichier `toto.txt` ne rentrent pas en conflit car ne porte pas sur les mêmes lignes du fichier, alors la fusion se fera sans aucune intervention de votre part. Vous pouvez vérifier le statut du dépôt, et vous devrez obtenir quelque chose comme ceci :

       ```sh
       ~/AutreRepertoire/MonProjet$ git status
       Sur la branche main
       Votre branche est en avance par rapport à 'origin/main' avec 2 commits.
       ```
      Notez que le dernier commit correspond à la fusion des 2 versions qui étaient en conflit.

   2. Si les deux historiques sont "incompatibles", alors le fichier `toto.txt` contiendra l'ensemble de modifications issues des deux dépôts (local et distant) et ce sera à vous de corriger le tout à la main : ouvrir le fichier `toto.txt` avec un éditeur de texte, regarder l'ensemble de conflits indiqués par des chevrons `<<<<<` et remplacer les par la version qui vous convient. Une fois le conflit résolu, vous devrez sauvegarder la résolution et diffuser ces modifications :
       ```sh
       ~/AutreRepertoire/MonProjet$ git add toto.txt
       ~/AutreRepertoire/MonProjet$ git commit -m "résolution du conflit"
       ~/AutreRepertoire/MonProjet$ git push
       ```

## Ignorer les fichiers inintéressants
La plupart du temps, lors qu'on développe il y a des fichiers qu'on souhaite **ignorer** en permanence dans les sauvegardes. Par exemple, si vous programmez en Java les fichiers '.class' ne devraient pas être versionnés pour plusieurs raisons : ils n'apportent aucune utilité à être suivi car ce sont des fichiers compilés non-destinés à la modification par le programmeur, ils polluent inutilement le dépôt... Cela peut être encore plus embêtant si vous versionnez des fichiers de configuration locale de votre machine : lorsqu'on les récupère sur une autre machine, avec un autre environnement, cela peut créer des problèmes de compatibilité.

Pour gérer cela facilement, vous devez créer le fichier '.gitignore' à la racine de votre projet. Dans ce fichier, vous pouvez ajouter tout ce que vous voulez ignorer dans vos commits. Par exemple le ['.gitignore'](.gitignore) du projet que vous êtes en train de lire, permet d'ignorer 3 extensions '.class', '.o', '.log'.

Lorsque vous créez ou modifiez le fichier '.gitignore', pensez à le versionner car il fait partie du dépôt ! Donc :
```sh
~/AutreRepertoire/MonProjet$ git add .gitignore
~/AutreRepertoire/MonProjet$ git commit -m "màj des fichiers à ignorer"
~/AutreRepertoire/MonProjet$ git push
```


## Un peu plus de cosmétique (optionnel)
Dans un terminal il n'y a pas d'information supplémentaire qui affiche si vous êtes dans un dépôt Git ou pas.
Pour faire plus joli, vous pouvez ajouter les lignes de commandes suivantes dans votre `~/.bashrc` pour afficher la branche courante:

```sh
    parse_git_branch() {
         git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/(\1)/'
    }
    export PS1="\u@\h \[\e[32m\]\w
    \[\e[91m\]\$(parse_git_branch)\[\e[00m\]$ "
```
plus d'infos [ici](https://medium.com/@thucnc/how-to-show-current-git-branch-with-colors-in-bash-prompt-380d05a24745)

## Et ce n'est pas tout !
Pour le moment on vous a montré les fonctionnalités de base de Git qui devraient être suffisantes pour vos TPs d'informatique. Sachez que la gestion de conflit a été survolé et plusieurs autres cas de conflits peuvent apparaître. Pour cela vos enseignants et vos moteurs de recherche seront vos amis !

Aussi nous n'avons pas évoqué la notion de [**branche** Git](https://git-scm.com/book/fr/v1/Les-branches-avec-Git-Ce-qu-est-une-branche). En effet, il est possible dans le cadre du même projet d'avoir plusieurs branches de développement (une par grande fonctionnalité ou une par développeur). Au fur et à mesure les branches sont _fusionnées_ pour intégrer les fonctionnalités dans la branche principale : _master_.
Pour le moment vous n'avez utilisé que cette branche, mais vous êtes encouragé à vous documenter et à apprendre par vous-mêmes l'utilisation des branches avec Git.

Vous pouvez suivre un tuto plus complet sur les différents aspects de Git ici : https://fr.wikibooks.org/wiki/Git/Principes

   **Remarque** : Depuis 2020-2021 sur GitLab et sur GitHub la branche par défaut ne s'appelle plus _master_ mais _main_ [pour éviter les stéréotypes raciaux sur leur plateforme](https://about.gitlab.com/blog/2021/03/10/new-git-default-branch-name/). Dans le logiciel Git installé sur votre machine, la branche par défaut continue de s'appeler _master_ pour le moment. Si après avoir initialisé un dépôt Git en local vous souhaitez utiliser le nom _main_ comme nom de la branche par défaut, il suffit de taper de faire un renommage :
   
```sh
~/AutreRepertoire/MonProjet$ git switch -c main
```

# Désormais...

Pour chaque nouvelle fonctionnalité programmée (par exemple pour chaque exercice de TP résolu), faites un `git add` et un `git commit`.
À la fin de la séance n'oubliez pas de pousser l'intégralité de vos sauvegardes (vos commits) sur un dépôt distant, en l'occurrence sur le serveur GitLab de l'IUT.
Pour chaque TD, vous pouvez créer un nouveau dépôt sur GitLab ou bien avoir un dépôt centralisé pour chaque matière et y versionner l'ensemble de travaux.
Maintenant, c'est à vous de vous organiser !
