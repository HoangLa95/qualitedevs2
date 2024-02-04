# Nommer et formatter

:::{note} Disclaimer
:class: dropdown
Tous les cours qui suivent vont vous donner une école de pensée sur le "code propre". Ce ne sont pas des règles absolues. Cependant, il faut maîtriser les règles avant de les enfreindre. Donc, pour le moment, **ces règles sont absolues**.
:::

```{figure} ../images/searching_meaningful_variable_name.png
:alt: Nom de variable expressif
:align: center

Quand je cherche un nom de variable expressif (Credits: thecodinglove.com)
```

## Nommer

### camelCase

```{figure} ../images/camelCase.png
:alt: camelCase
:align: center

camelCase (Credits: wikipedia.com)
```

:::{note} camelCase
:class: dropdown
Nous allons utiliser la convention **camelCase** où la première lettre de la variable commence toujours par une minuscule et tous les mots qui suivent commencent par une majuscule.
:::

:::{seealso} VARIABLES_GLOBALES
:class: dropdown
Les variables globales doivent être en majuscule et les mots sont séparés par des underscores(`_`). 
:::

### Utiliser des noms révélateurs d'intention

Un nom de variable doit répondre aux trois questions suivantes :
1. Pourquoi il existe ?
2. Qu'est-ce qu'il fait ?
3. Comment est-il utilisé ?

:::{error} Commenter un nom de variable.
:class: dropdown
Si un nom a besoin d'un commentaire alors ce nom n'est pas révélateur d'intention.
:::

```{code} cpp
int d; // temps écoulé en jours
```
:::{hint} Quel nom choisir dans ce cas-ci ?
:class: dropdown
```{code} cpp
int elapsedTimeInDays;
int daysSinceCreation;
int daysSinceModification;
int fileAgeInDays;
```
:::

Un nom qui révèle l'intention d'une variable éclaircit énormément le code. Que fait le code suivant ? 

```{code} cpp
vector<vector<int>> getThem(const vector<vector<int>>& theList) {
    vector<vector<int>> list1;
    
    for(const auto& x : theList) {
        if(x[0] == 4) {
            list1.push_back(x);
        }
    }

    return list1;
}
```
:::{error} Problèmes avec ce code.
:class: dropdown
1. Que trouve-t-on dans `theList` ?
2. Quel rôle joue l'index `0` de `x` dans `theList` ?
3. Quel rôle joue la valeur `4` ?
4. Que fais-je avec la liste retourné ?
:::

:::{hint} Minesweeper[^minesweeper]
:class: dropdown
C'est un morceau de code du jeu *Minesweeper* dans lequel `theList` est le plateau de jeu, chaque case est représenté par un `vector`, `x[0]` est le statut de la case, `4` est la présence d'un drapeau. 
:::

[^minesweeper]: ![Minesweeper](../images/minesweeper.png)

Une première réécriture :
```{code} cpp
const int STATUS_VALUE = 0;
const int FLAGGED = 4;

vector<vector<int>> getFlaggedCells(const vector<vector<int>>& gameBoard) {
    vector<vector<int>> flaggedCells;
    
    for(const auto& cell : gameBoard) {
        if(cell[STATUS_VALUE] == FLAGGED) {
            flaggedCells.push_back(cell);
        }
    }

    return flaggedCells;
}
```

:::{seealso} Pour aller plus loin.
:class: dropdown
```{code} cpp
class Cell {
public:
    static const int STATUS_VALUE = 0;
    static const int FLAGGED = 4;

    vector<int> values;

    // Constructeur
    //...

    bool isFlagged() {
        return values[STATUS_VALUE] == FLAGGED;
    }
};

vector<Cell> getFlaggedCells(const vector<Cell>& gameBoard) {
    vector<Cell> flaggedCells;

    for(const auto& cell : gameBoard) {
        if(cell.isFlagged()) {
            flaggedCells.push_back(cell);
        }
    }

    return flaggedCells;
}
```
:::

### Ne pas propager la désinformation

C'est quoi le problème avec les codes suivants ?

```{code} cpp
vector<Account> accountList;
```

:::{error} Problème 
:class: dropdown
Il ne faut pas nommer un `vector` "List" quand `list` est aussi un type de C++. Une solution possible :
```{code} cpp
vector<Account> accountVector;
vector<Account> accounts;
```
:::

```{code} cpp
int controllerForEfficientHandlingOfStrings;
int controllerForEfficientStorageOfStrings;
```

:::{error} Problème 
:class: dropdown
Il ne faut pas utiliser des noms qui sont trop similaires. En plus, avec l'auto-complétion, vous n'allez pas voir la différence.
:::

```{code} cpp
int a = 1;
if(O == l) {
    a = O1;
}
else {
    l = 01;
}
```
:::{error} Problème 
:class: dropdown
Même si la coloration syntaxique permet de voir la différence entre `O` et `0`, et entre `1` et `l`. Là vous êtes juste en train de faire de l'obstruction de code... 
:::


## Formatter