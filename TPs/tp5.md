# TP5 : Gestion des erreurs et intégration Git dans VSCodium

(tp5-objectifs)=
Le but de ce TP est de comprendre les points suivants :
- [ ] [La gestion des erreurs](#exercice-1--error-handling)
- [ ] [`git graph`](#alias)
- [ ] [`git checkout`](#checkout)
- [ ] [`git revert`](#revert)
- [ ] [`git reset`](#reset)
- [ ] [Intégration Git dans VSCodium](#git-avec-ide)

## Exercice 1 : Error handling

:::{warning} Disclaimer
:class: dropdown
Nous allons nous concentrer sur l'écriture et le fonctionnement des gestionnaires d'erreur dans cet exercice sans se soucier de réorganiser le code, les fichiers et la compilation automatique.
:::

1. Créez un répertoire `error-handling` dans `TP5` qui contient les fichiers: `level1`, `level2`, `level3` et `play.cpp`.

2. Dans un fichier `play.cpp`, recopiez le code suivant.

```{code} cpp
#include <iostream>
#include <string>
#include <fstream>

int getLevelFromUser() {
    int userInput;
    std::cout << "Enter a game level: ";
    std::cin >> userInput;
    return userInput;
}

std::string getLevelData(int level) {
    std::string filepath = "level" + std::to_string(level);
    std::ifstream file(filepath);
    std::string levelData;
    levelData.assign((std::istreambuf_iterator<char>(file)),std::istreambuf_iterator<char>());
    file.close();
    return levelData;
}

void play() {
    int level = getLevelFromUser();
    getLevelData(level);
    std::cout << "You are playing level " + std::to_string(level) + ".\n";
}

int main() {
    play();
    return 0;
}
```

:::{hint} Que fait ce code ?
:class: dropdown
Nous allons simuler une portion de code d'un jeu où :
- `getLevelFromUser` récupère le niveau du jeu à partir d'une entrée utilisateur.
- `getLevelData(int level)` récupère les ressources pour le niveau `level` en ouvrant le fichier `level1` par exemple si `level` est `1`. Le contenu de `level1` est ensuite recopié dans `string levelData` avec la fonction `assign`. Nous fermons le fichier avec `file.close()` et retournons `levelData`.
- `play` récupère le numéro du niveau, les ressources correspondant et nous simulons le gameplay avec le message `You are playing level 1.` (si `level` est 1).
Les fichiers `level1`, `level2` et `level3` ont juste besoin d'exister pour notre simulation.
:::

3. Compilez et exécutez le code pour observer son comportement selon l'entrée utilisateur.

4. Dans `getLevelFromUser`, écrivez un validateur de l'entrée qui autorise seulement 3 tentatives et l'entrée doit être un entier entre `firstLevel` et `lastLevel` que nous pouvons par exemple fixer à 1 et 5 respectivement. Au delà de 3 tentatives, la fonction doit lancer une erreur.

Nous n'allons pas gérer cette erreur tout de suite car dans ce cas-ci, nous avons envie de retourner vers le menu principal du jeu, ce qui ne devrait pas être accessible depuis `getLevelFromUser`. La gestion des erreurs sans interrompre le programme est toujours préférable à arrêter le programme avec un code d'erreur.

5. Attraper l'erreur dans `play` et simuler le retour vers le menu principal avec un message dans `cout` qui dit `Returning to main menu.`

6. Dans `getLevelData`, nous allons vérifier si le fichier correspondant au niveau existe. S'il n'existe pas (`if(!file) {...}`), alors nous retournons le message `"Resources not found for level <numéro du niveau>."` et arrête le programme avec le code `EXIT_FAILURE`. 

Il s'agit d'une gestion d'erreur locale sans avoir à lancer l'erreur pour l'attraper ailleurs, ce qui simplifie la logique du code.

## Exercice 2 : Git integration

Créez un répertoire `git-integration`.

### Git sans IDE

D'abord, nous allons apprendre à utiliser les commandes suivantes dans le terminal.

#### log

1. Exécutez `git log --oneline` pour voir l'historique du dépôt où chaque commit correspond à une ligne qui commence avec un code, appelé **commit hash**, permettant d'identifier le commit puis le message du commit.

:::{hint} `q`
:class:dropdown
Un petit rappel : `q` pour quitter le log.
:::

2. Exécutez `git log --online --graph` pour voir le graphe (dessiné en [ASCII](https://fr.wikipedia.org/wiki/American_Standard_Code_for_Information_Interchange) dans le terminal) associé à ces commits, où chaque sommet est représenté par une étoile.

Votre graphe devrait être plutôt linéaire (un chemin) à part les endroits où vous avez eu des conflits et donc des branches divergentes.

:::{important} HEAD et origin/HEAD
**HEAD** est le sommet courant où se trouve votre dépôt local (pas votre répertoire de travail mais le dépôt local où vous avez fait les commits). De même pour **origin/HEAD** pour le dépôt distant.
:::

#### alias

Quand vous allez apprendre à gérer plusieurs branches dans votre dépôt, vous pouvez aussi ajouter les options `--decorate` (pour avoir le nom de la branche à côté des commits) et `--all` (pour voir toutes les branches) à `git log --oneline --graph`.

1. Définissez la commande `git graph` avec un **alias** de la façon suivante :
```{code} sh
git config --global alias.graph "log --oneline --graph --decorate --all"
```

2. Exécutez `git graph`.

:::{important} `git status` et `git graph`
Dans la suite, exécutez `git status` et `git graph` après chaque manipulation pour comprendre ce qui se passe !
:::

#### show

1. Vous pouvez voir tous les détails d'un commit particulier avec `git show <commit hash>`.

#### checkout

Nous pouvons visiter les différents sommets de ce graphe, ce qui veut dire que nous pouvons revenir aux différents états de notre dépôt, des états qui sont loggés par les commits.

1. Exécutez `git checkout <commit hash>` avec un commit hash d'un vieux commit. Votre commande devrait ressembler à `git checkout abc0123` par exemple.

:::{important} Detached HEAD
Quand vous checkout un vieux commit, vous êtes en état **detached HEAD** où les changements et commit que vous faites dans cet état vont être perdu.
Pour garder ces changements, il faut créer une autre branche. Nous n'allons pas voir ces manipulations dans ce TP.
:::

2. Observez l'état de votre (ancien) dépôt.

3. Revenez au sommet le plus récent (et sortez de l'état detached HEAD) en faisant `git checkout main`. 

:::{seealso} `git checkout -`
:class:dropdown
`git checkout -` permet de revenir au dernier sommet visité (avant le sommet courant où se trouve HEAD).
:::

#### revert

1. Créez un fichier `revert.txt` qui contient une ligne `Hello World!` et commit ce nouveau changement.

2. Ajoutez une deuxième ligne à `revert.txt` qui dit `Hi!` et commit ce nouveau changement.

Imaginons que la deuxième ligne ajoutée était une erreur. Nous allons **revert** ce commit tout en gardant le commit erroné dans l'historique parce qu'il se peut que ce commit erroné contient des informations utiles ou il est déjà synchronisé avec le dépôt distant (si vous avez fait un push).

3. Exécutez `git revert HEAD --no-edit` puis regardez le contenu de `revert.txt`.

Vous pouvez voir qu'un nouveau commit a été créé avec le message par défaut `Revert "<message du commit annulé>"`. Vous auriez aussi pu faire `git revert HEAD -m "<message>"` pour mettre un message personalisé au lieu du message par défaut.

Nous pouvons aussi revert plusieurs commits. Chaque commit sera accompagné d'un revert commit.

4. Exécutez `git revert HEAD~3..HEAD --no-edit` pour revert les trois commits les plus récents.

Vous pouvez enlever l'option `--no-edit` pour écrire un message par revert commit. Vous auriez aussi pu faire `git revert -n HEAD~3..HEAD` qui ne fait pas automatiquement plusieurs commit puis `git commit -m "<message>"` pour écrire un seul commit (au lieu de trois) avec le message que vous voulez.

:::{danger} Revert un vieux commit
Il est très dangereux de revert un commit `A` particulier dans le passé (si ce n'est pas le dernier commit) car vous allez avoir des conflits avec tous les commits qui suivent `A` et qui modifie des fichiers modifiés par `A`. Il est donc conseillé de faire que des revert avec `HEAD` ou `HEAD~<nombre>..HEAD`. Sinon, il faut résoudre les conflits manuellement, ajouter les modifications, puis `git revert --continue`. Si vous êtes perdu, vous pouvez aussi faire `git revert --abort` pour annuler l'opération.
:::

#### reset

1. Créez un fichier `reset.txt` avec `Hello World!` et commit ce changement **sans push**.

2. Ajoutez une deuxième ligne à `reset.txt` qui dit `Hi!` et commit ce nouveau changement **sans push**.

La commande `git reset` fonctionne similairement à revert mais elle supprime un commit `A` de l'historique au lieu de créer un commit supplémentaire qui revert `A`.

3. Exécutez `git reset HEAD~1` pour reset le dernier commit.

Vous pouvez aussi faire `git reset HEAD~<nombre>` pour reset les `<nombre>` derniers commits.

:::{important} `--soft`, `--mixed` et `--hard`
`git reset` a trois options principales qui sont `--soft`, `--mixed` et `--hard` :
- `--soft` reset vos commits mais gardent l'état de leurs changements comme changements suivis (staged changes) pour les différencier des changements courant qui ne sont pas encore suivis.
- `--mixed` est le comportement par défaut de reset qui garde les changements de ces commits mais les mélange aussi avec les changements dans votre répertoire de travail.
- `--hard` supprime les commits et leurs changements.
:::

:::{danger} Reset d'un commit synchronisé
Si un commit existe déjà sur votre dépôt distant, il est très dangereux de faire un reset parce que reset modifie l'historique. Cela implique que vous devez écraser l'historique du dépôt distant avec l'historique du dépôt local. Ceci est possible avec l'option `--force` de push mais la branche `main` est toujours protégée par défaut (impossible de faire `--force`). Dans ce cas-ci, **il faut utiliser revert** à la place de reset.
:::

### Git avec IDE

Vous pouvez maintenant ouvrir votre dépôt Git avec VSCodium.
Si VSCodium vous demande si vous voulez faire `git fetch` régulièrement, vous pouvez répondre oui. Si vous avez répondu non, ce n'est pas grave. Nous allons expliquer ce que fait `git fetch` plus bas.

1. Dans la barre verticale à gauche, vous avez **Extensions**. Si vous voyez l'erreur **Error while fetching extensions. XHR failed** sous Debian à l'IUT, consultez le [soutien technique sur VSCodium](#vscodium-proxy) pour régler le proxy.

2. Cherchez et installez l'extension **Git Graph** de **mhutchie**.

#### log

1. Ouvrez **Git Graph** avec le bouton Git Graph en bas de votre écran ou en cliquant (dans la barre en haut) sur **View** > **Command Palette** (raccourci `F1` ou `Ctrl+Shift+P`) puis tapez `Git Graph` et choisissez l'option **Git Graph: View Git Graph**.

:::{seealso} Command Palette
:class: dropdown
Vous pouvez tout faire dans VSCodium avec la Command Palette, y compris toutes les commandes Git.
:::

Le graphe de l'extension Git Graph représente votre log et les commit hash sont à droite.

#### show

1. Cliquez sur un sommet du graphe pour voir tous les détails sur le commit correspondant.

2. Cliquez sur un fichier modifié d'un commit pour voir les changements précis (comme avec `git diff`).

#### status

1. Vous pouvez voir le statut de votre dépôt dans **Source Control** dans la barre verticale à gauche.

#### add

1. Créez un nouveau fichier.

2. Vous pouvez ajouter le nouveau changement dans Source Control avec **+** à côté du changement ou ajouter tous les changements avec **+** en haut.

#### restore

1. Pour ne plus suivre un changement (unstage), vous pouvez cliquez sur **-** à côté d'un changement suivi (staged change).

2. Pour supprimer les nouveaux changements qui ne sont pas encore suivis dans un fichier, vous pouvez cliquez sur **la flèche à gauche de +** qui indique **Discard Changes**.

#### commit

1. Dans la boîte **Message** en haut de Source Control, vous pouvez écrire votre message de commit.

2. Cliquez sur le bouton **Commit** pour faire un commit.

#### push

1. Cliquez sur **Sync Changes** pour faire un push.

#### pull

1. Dans Source Control, juste au dessus de Message, quand vous survolez avec votre souris, il y a `...` qui indique **More Actions**.

2. À l'intérieur de ce menu dropdown, vous pouvez trouvez **Pull** parmi d'autres commandes Git. 

Vous pouvez aussi utiliser la **Command Palette** (`F1`), tapez `pull` et choisir l'option **Git: Pull**.

#### clone

1. Similairement à pull, vous pouvez faire Clone de la même façon et rentrez l'adresse du dépôt (avec le PAT).

#### config

1. Dans les boutons à droite de Git Graph, vous pouvez trouver un bouton **Repository Settings** avec les configurations du dépôt.

#### fetch/remote update

`git fetch` fait (presque) la même chose que `git remote update` mais permet d'être plus précis avec `git fetch <branche>` si vous voulez récupérer les informations sur une branche particulière.

Par défaut `git fetch` récupère seulement les informations sur le dépôt distant appelé `origin`. Dans le futur, si vous allez contribuer à des projets existant développés par d'autres personnes, vous pouvez être amené à faire des **forks** qui sont des copies de ces projets avec vos propres dépôts distant et local. Dans ce cas-ci, il est utile de récupérer les changements de plusieurs dépôts distants différent. C'est ce que fait `git remote update` (ou de façon équivalente `git fetch --all`).

1. Cliquez sur le bouton **Fetch from Remote(s)** parmi les boutons à droite de Git Graph.

Parfois, il est utile de faire **Refresh** pour rafraîchir la vue du graphe s'il y a eu des changements récemment.

#### checkout

1. Dans Git Graph, cliquez droit sur un message à côté d'un commit et choisissez l'option **Checkout**.

2. Revenez au commit le plus récent en cliquant droit sur l'icône de la branche `main` (à côté du commit le plus récent) et en choisissant l'option **Checkout Branch**.

#### revert

1. Dans Git Graph, cliquez droit sur le message du commit le plus récent et choisissez **Revert**.

#### reset

1. Dans Git Graph, cliquez droit sur le message d'un ancien commit et choisissez **Reset current branch to this Commit...** puis l'une des options **Soft**, **Mixed** ou **Hard** (préférablement Soft ou Mixed).

#### merge

1. Créez un fichier `merge.txt` vide et synchroniser ce fichier pour l'avoir dans vos deux dépôts.

2. Localement, ajoutez `Hello World!` sur la première ligne du fichier.

3. Sur GitLab, utilisez le Web IDE pour ajouter `Hi!` sur la première ligne du fichier.

:::{seealso} Web IDE de GitLab
:class: dropdown
Le Web IDE de GitLab est basé sur VSCode/VSCodium donc son intégration Git est très similaire mais il est sans extension. Vous pouvez toujours utiliser la Command Palette.
:::

4. Fetch/remote update dans Git Graph et observez les branches divergentes.

5. Cliquez sur le message du commit de la branche du dépôt distant et choisissez **Merge into current branch...** avec l'option **No commit**.

6. Ouvrez le fichier `merge.txt`.

7. Vous pouvez choisir une des options proposées au dessus du conflit ou aucune option et résoudre le conflit vous-même avec un nouveau texte comme `Bye!`.

Vous pouvez aussi choisir l'option **Open in merge editor** qui est proposée en bas à droite qui ouvre trois écrans avec les deux versions du fichier avec le conflit et une troisième version qui est la version finale que vous allez écrire.

8. Finissez la synchronisation avec la version fusionnée.

:::{seealso} `git merge --abort`
:class: dropdown
`git pull` fait la même chose que `git fetch` puis `git merge`. Quand vous avez oublié de regardez l'état des dépôts avant de faire un pull ou une merge, vous pouvez utiliser `git merge --abort` pour annuler la fusion.
:::

Revenez aux [objectifs](#tp5-objectifs) et cochez les points que vous avez maîtrisés. Revenez sur les points que vous n'avez pas encore bien compris. Appelez votre encadrant si besoin.