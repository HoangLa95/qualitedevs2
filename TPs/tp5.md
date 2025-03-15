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
Nous allons nous concentrer sur l'écriture et le fonctionnement des gestionnaires d'erreurs dans cet exercice, sans nous préoccuper de la réorganisation du code, des fichiers et de la compilation automatique.
:::

1. Créez un répertoire `error-handling` dans `TP5`, puis ajoutez les fichiers suivants : `level1`, `level2`, `level3` et `play.cpp`.

2. Dans le fichier `play.cpp`, copiez le code suivant.

```{code} cpp
:filename: play.cpp
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
Nous allons simuler une partie du code d'un jeu où :
- `getLevelFromUser` récupère le niveau du jeu à partir de l'entrée utilisateur.
- `getLevelData(int level)` obtient les ressources pour le niveau `level` en ouvrant le fichier `level1` par exemple si `level` vaut `1`. Le contenu de `level1` est ensuite copié dans `string levelData` à l'aide de la fonction `assign`. Le fichier est ensuite fermé avec `file.close()` et nous retournons `levelData`.
- `play` récupère le numéro du niveau et les ressources associées, puis simule le gameplay avec le message `You are playing level 1.` (si `level` est 1).
Les fichiers `level1`, `level2` et `level3` doivent simplement exister pour cette simulation.
:::

3. Compilez et exécutez le code pour observer son comportement en fonction de l'entrée utilisateur.

4. Dans `getLevelFromUser`, implémentez un validateur d'entrée qui autorise seulement 3 tentatives, avec un entier compris entre `firstLevel` et `lastLevel` (par exemple, de 1 à 5). Après 3 tentatives échouées, la fonction doit lancer une exception.

Nous ne gèrerons pas cette erreur tout de suite car nous souhaitons, dans ce cas, revenir au menu principal du jeu, ce qui ne doit pas être accessible depuis `getLevelFromUser`. La gestion des erreurs sans interrompre le programme est toujours préférable à l'arrêt du programme avec un code d'erreur.

5. Attrapez l'erreur dans `play` et simulez un retour au menu principal avec un message dans `cout` indiquant `Returning to main menu.`

6. Dans `getLevelData`, nous allons vérifier si le fichier correspondant au niveau existe. S'il n'existe pas (`if(!file) {...}`), nous retournons le message `"Resources not found for level <numéro du niveau>."` et arrêtons le programme avec le code `EXIT_FAILURE`. 

Il s'agit d'une gestion d'erreur locale, évitant de lancer l'exception pour la gérer ailleurs, ce qui simplifie la logique du code.

## Exercice 2 : Intégration Git

Créez un répertoire `git-integration`.

### Git sans IDE

Tout d'abord, nous allons apprendre à utiliser les commandes suivantes dans le terminal.

#### log

1. Exécutez `git log --oneline` pour afficher l'historique du dépôt où chaque commit correspond à une ligne commençant par un code, appelé **commit hash**, permettant d'identifier le commit, suivi du message du commit.

:::{hint} `q`
:class: dropdown
Petit rappel : `q` pour quitter le log.
:::

2. Exécutez `git log --oneline --graph` pour voir le graphe (dessiné en [ASCII](https://fr.wikipedia.org/wiki/American_Standard_Code_for_Information_Interchange) dans le terminal) associé à ces commits, où chaque sommet est représenté par une étoile.

Votre graphe devrait être plutôt linéaire (un chemin), sauf aux endroits où vous avez eu des conflits, créant ainsi des branches divergentes.

:::{important} HEAD et origin/HEAD
**HEAD** représente le sommet courant où se trouve votre dépôt local (pas votre répertoire de travail, mais le dépôt local où vous avez effectué les commits). De même pour **origin/HEAD** pour le dépôt distant.
:::

#### alias

Lorsque vous apprendrez à gérer plusieurs branches dans votre dépôt, vous pourrez ajouter les options `--decorate` (pour afficher le nom de la branche à côté des commits) et `--all` (pour voir toutes les branches) à la commande `git log --oneline --graph`.

1. Définissez la commande `git graph` en tant qu'**alias** de la manière suivante :
```{code} sh
git config --global alias.graph "log --oneline --graph --decorate --all"
```

2. Exécutez `git graph`.

:::{important} `git status` et `git graph`
Dans la suite de l'exercice, exécutez `git status` et `git graph` après chaque manipulation pour mieux comprendre ce qui se passe !
:::

#### show

1. Vous pouvez voir tous les détails d'un commit particulier avec `git show <commit hash>`.

#### checkout

Nous pouvons naviguer à travers les différents sommets de ce graphe, ce qui signifie que nous pouvons revenir à différents états de notre dépôt, enregistrés par les commits.

1. Exécutez `git checkout <commit hash>` avec le commit hash d'un ancien commit. Votre commande pourrait ressembler à `git checkout abc0123` par exemple.

:::{important} Detached HEAD
Lorsque vous effectuez un checkout sur un ancien commit, vous entrez dans un état **detached HEAD**, où les changements et commits réalisés seront perdus. 
Pour conserver ces changements, il est nécessaire de créer une nouvelle branche. Nous ne couvrirons pas cette manipulation dans cet exercice.
:::

2. Observez l'état de votre (ancien) dépôt.

3. Revenez au sommet le plus récent (et quittez l'état detached HEAD) en exécutant `git checkout main`. 

:::{seealso} `git checkout -`
:class:dropdown
La commande `git checkout -` permet de revenir au dernier sommet visité (avant le sommet courant où se trouve HEAD).
:::

#### revert

1. Créez un fichier `revert.txt` contenant la ligne `Hello World!` et effectuez un commit avec ce changement.

2. Ajoutez une deuxième ligne à `revert.txt` contenant `Hi!` et effectuez un commit avec ce changement.

Imaginons que la deuxième ligne ajoutée soit une erreur. Nous allons **revert** ce commit tout en conservant l'historique, car il se peut que ce commit erroné contienne des informations utiles ou qu'il soit déjà synchronisé avec le dépôt distant (si vous avez effectué un push).

3. Exécutez `git revert HEAD --no-edit`, puis vérifiez le contenu de `revert.txt`.

Vous constaterez qu'un nouveau commit a été créé avec le message par défaut `Revert "<message du commit annulé>"`. Vous auriez aussi pu utiliser `git revert HEAD -m "<message>"` pour personnaliser le message au lieu d'utiliser le message par défaut.

Nous pouvons également effectuer un revert de plusieurs commits. Chaque reverted commit sera accompagné d’un commit de revert.

4. Exécutez `git revert HEAD~3..HEAD --no-edit` pour revert les trois derniers commits.

Vous pouvez omettre l'option `--no-edit` pour ajouter un message pour chaque reverted commit. Vous auriez aussi pu utiliser `git revert -n HEAD~3..HEAD`, ce qui ne crée pas automatiquement plusieurs commits, puis exécuter `git commit -m "<message>"` pour créer un seul commit (au lieu de trois) avec le message que vous souhaitez.

:::{danger} Revert un ancien commit
Il est risqué de revert un commit `A` particulier dans l'historique (si ce n'est pas le dernier commit), car cela pourrait entraîner des conflits avec tous les commits qui suivent `A` et qui modifient des fichiers modifiés par `A`. 

Il est donc conseillé de ne faire que des reverts avec `HEAD` ou `HEAD~<nombre>..HEAD`. 

Si vous devez revert un commit plus ancien, vous devrez résoudre les conflits manuellement, ajouter les modifications, puis utiliser `git revert --continue`. Si vous êtes perdu, vous pouvez également utiliser `git revert --abort` pour annuler l'opération.
:::

#### reset

1. Créez un fichier `reset.txt` contenant `Hello World!` et effectuez un commit de ce changement **sans push**.

2. Ajoutez une deuxième ligne à `reset.txt` disant `Hi!` et effectuez un commit de ce changement **sans push**.

La commande `git reset` fonctionne de manière similaire à `git revert`, mais elle supprime un commit `A` de l'historique au lieu de créer un commit supplémentaire pour annuler `A`.

3. Exécutez `git reset HEAD~1` pour annuler le dernier commit.

Vous pouvez également exécuter `git reset HEAD~<nombre>` pour annuler les `<nombre>` derniers commits.

:::{important} `--soft`, `--mixed` et `--hard`
`git reset` dispose de trois options principales : `--soft`, `--mixed` et `--hard`.

- `--soft` annule les commits tout en conservant leurs changements comme des changements suivis (staged changes), ce qui les distingue des changements non suivis dans votre répertoire de travail.
- `--mixed` est le comportement par défaut de `reset` qui conserve les changements des commits annulés tout en les mélangeant avec les modifications présentes dans votre répertoire de travail.
- `--hard` supprime à la fois les commits annulés et leurs changements.
:::

:::{danger} Reset d’un commit déjà synchronisé
Si un commit a déjà été poussé vers votre dépôt distant, il est risqué d'utiliser `reset`, car cela modifie l'historique. Cela impliquerait d'écraser l'historique du dépôt distant avec l'historique local. Bien que cela soit possible avec l'option `--force` de `git push`, la branche `main` est protégée par défaut (ce qui empêche un `--force`). Dans ce cas, **il faut utiliser `revert`** plutôt que `reset`.
:::

### Git avec IDE

Vous pouvez maintenant ouvrir votre dépôt Git avec VSCodium.  
Si VSCodium vous demande si vous souhaitez effectuer régulièrement un `git fetch`, vous pouvez répondre oui. Si vous avez répondu non, ce n'est pas grave, nous expliquerons ce que fait `git fetch` ci-dessous.

1. Dans la barre verticale à gauche, vous trouverez **Extensions**. Si vous voyez l'erreur **Error while fetching extensions. XHR failed** sous Debian à l'IUT, consultez le [soutien technique sur VSCodium](#vscodium-proxy) pour configurer le proxy.

2. Cherchez et installez l'extension **Git Graph** de **mhutchie**.

#### log

1. Ouvrez **Git Graph** en cliquant sur le bouton Git Graph en bas de votre écran ou en sélectionnant **View** > **Command Palette** (raccourci `F1` ou `Ctrl+Shift+P`), puis tapez `Git Graph` et choisissez l'option **Git Graph: View Git Graph**.

:::{seealso} Command Palette
:class: dropdown
Vous pouvez effectuer toutes les actions dans VSCodium via la Command Palette, y compris toutes les commandes Git.
:::

Le graphe affiché par l'extension Git Graph représente votre log, avec les hash des commits à droite.

#### show

1. Cliquez sur un sommet du graphe pour voir tous les détails du commit correspondant.

2. Cliquez sur un fichier modifié d’un commit pour voir les changements spécifiques (comme avec `git diff`).

#### status

1. Vous pouvez voir le statut de votre dépôt dans **Source Control** dans la barre verticale à gauche.

#### add

1. Créez un nouveau fichier.

2. Vous pouvez ajouter le nouveau changement dans Source Control en cliquant sur **+** à côté du changement ou ajouter tous les changements en cliquant sur **+** en haut.

#### restore

1. Pour ne plus suivre un changement (unstage), vous pouvez cliquer sur **-** à côté d'un changement suivi.

2. Pour supprimer les nouveaux changements non suivis dans un fichier, vous pouvez cliquer sur **la flèche à gauche de +**, indiquant **Discard Changes**.

#### commit

1. Dans la boîte **Message** en haut de Source Control, écrivez votre message de commit.

2. Cliquez sur le bouton **Commit** pour effectuer le commit.

#### push

1. Cliquez sur **Sync Changes** pour pousser vos changements.

#### pull

1. Dans Source Control, juste au-dessus de la boîte de message, survolez avec votre souris **...** pour afficher **More Actions**.

2. Dans ce menu déroulant, vous trouverez **Pull** parmi d'autres commandes Git. 

Vous pouvez aussi utiliser la **Command Palette** (`F1`), tapez `pull` et sélectionnez l'option **Git: Pull**.

#### clone

1. De manière similaire à pull, vous pouvez cloner un dépôt de la même façon en entrant l'adresse du dépôt (avec le PAT).

#### config

1. Dans les boutons à droite de Git Graph, vous trouverez un bouton **Repository Settings** où vous pourrez configurer les paramètres de votre dépôt.

#### fetch/remote update

`git fetch` fait (presque) la même chose que `git remote update`, mais permet d'être plus précis avec `git fetch <branche>` si vous souhaitez récupérer les informations d'une branche spécifique.

Par défaut, `git fetch` récupère uniquement les informations du dépôt distant nommé `origin`. Dans le futur, si vous contribuez à des projets existants développés par d'autres personnes, vous devrez probablement effectuer des **forks**, qui sont des copies de ces projets avec vos propres dépôts distants et locaux. Dans ce cas, il est utile de récupérer les changements de plusieurs dépôts distants. Cela se fait avec `git remote update` (ou de manière équivalente, `git fetch --all`).

1. Cliquez sur le bouton **Fetch from Remote(s)** parmi les boutons à droite de Git Graph.

Parfois, il est utile de faire un **Refresh** pour mettre à jour la vue du graphe si des changements ont eu lieu récemment.

#### checkout

1. Dans Git Graph, faites un clic droit sur un message à côté d’un commit et choisissez l'option **Checkout**.

2. Revenez au commit le plus récent en faisant un clic droit sur l'icône de la branche `main` (à côté du commit le plus récent) et choisissez l'option **Checkout Branch**.

#### revert

1. Dans Git Graph, faites un clic droit sur le message du commit le plus récent et choisissez **Revert**.

#### reset

1. Dans Git Graph, faites un clic droit sur le message d'un ancien commit et choisissez **Reset current branch to this Commit...**, puis sélectionnez l'option **Soft**, **Mixed** ou **Hard** (préférablement Soft ou Mixed).

:::{danger} Ne pas reset un commit synchronisé !
:class: dropdown
Rappel !
:::

#### merge

1. Créez un fichier `merge.txt` vide et synchronisez ce fichier pour l’avoir dans vos deux dépôts.

2. Localement, ajoutez `Hello World!` sur la première ligne du fichier.

3. Sur GitLab, utilisez le Web IDE pour ajouter `Hi!` sur la première ligne du fichier.

:::{seealso} Web IDE de GitLab
:class: dropdown
Le Web IDE de GitLab est basé sur VSCode/VSCodium, donc son intégration Git est très similaire mais sans extensions. Vous pouvez toujours utiliser la Command Palette.
:::

4. Effectuez un fetch dans Git Graph et observez les branches divergentes.

5. Faites un clic droit sur le message du commit de la branche du dépôt distant et choisissez **Merge into current branch...** avec l'option **No commit**.

6. Ouvrez le fichier `merge.txt`.

7. Vous pouvez choisir une des options proposées au-dessus du conflit, ou ne pas en choisir et résoudre le conflit vous-même avec un texte qui remplace les deux autres comme `Bye!`.

Vous pouvez également choisir l'option **Open in merge editor** qui s'affiche en bas à droite. Cela ouvrira trois écrans : deux avec les versions du fichier ayant un conflit et un troisième avec la version finale que vous écrirez.

8. Terminez la synchronisation avec la version fusionnée.

:::{seealso} `git merge --abort`
:class: dropdown
`git pull` fait la même chose que `git fetch` suivi de `git merge`. Lorsque vous oubliez de vérifier l'état des dépôts avant d'effectuer un pull ou une merge, vous pouvez utiliser `git merge --abort` pour annuler la fusion.
:::

Retournez aux [objectifs](#tp5-objectifs) et cochez les points que vous avez maîtrisés. Reprenez les points que vous n'avez pas encore bien compris. Appelez votre encadrant si besoin.