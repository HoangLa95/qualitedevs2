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

:::{seealso} Autres conventions
:class: dropdown
1. `VARIABLE_GLOBALE` :
Les variables globales doivent être en majuscule et les mots sont séparés par des underscores(`_`).
2. `class Object` :
Une classe doit commencer par une majuscule.
:::

### Utiliser des noms révélateurs d'intention

Un nom de variable (entre autre) doit répondre aux trois questions suivantes :
1. Pourquoi elle existe ?
2. Qu'est-ce qu'elle fait ?
3. Comment est-elle utilisée ?

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

### Utiliser des noms différents

```{code} cpp
void copyString(string a1, string a2, int length) {
    for (int i = 0; i < length; i++) {
        a2[i] = a1[i];
    }
}
```
:::{error} Problème 
:class: dropdown
Éviter de rajouter des chiffres après les noms de variables pour les distinguer. Une solution simple est la suivante :
```{code} cpp
void copyString(string source, string destination, int length) {
    for (int i = 0; i < length; i++) {
        destination[i] = source[i];
    }
}
```
:::

```{code} cpp
money;
moneyAmount;

message;
theMessage;

getProductInfo();
getProductData();

class Customer {
    //...
};
class CustomerObject {
    //...
};
```
:::{error} Problème 
:class: dropdown
Utiliser des noms avec des différences significatives.
:::

### Utiliser des noms prononçables

```{code} cpp
class DtaRcrd102 {
private:
    Date genymdhms;
    Date modymdhms;
    const string pszqint = "102";
//...
};
```
:::{error} Problème 
:class: dropdown
Ne pas utiliser des noms impossibles à prononcer. `genymdhms` est la date à laquelle l'objet a été *gen*(éré) et cette date est en *y*(ears), *m*(onths), *d*(ays), *h*(ours), *m*(inutes), *s*(econds). Vous ne pourriez pas communiquer à l'intérieur d'une équipe si vous n'arrivez pas à prononcer les noms de variables. Une solution pour transformer ce code :
```{code} cpp
class Customer {
private:
    Date generationTimeStamp;
    Date modificationTimeStamp;
    const string recordId = "102";
//...
};
```
:::

### Utiliser des noms recherchables

Imagine un code avec que des variables d'une seule lettre et des nombres fixés qui apparaissent partout et qu'il contient le morceau de code suivant.

```{code} cpp
for (int j=0; j<34; j++) {
    s += t[j]*4*5;
}
```

Maintenant, comparer-le à ce même code.

```{code} cpp
const int NUMBER_OF_TASKS = 34;
const int WORK_DAYS_PER_WEEK = 5;
const int NUMBER_OF_TASKS_PER_DAY = 4;
int totalCost = 0;
for (int j=0; j < NUMBER_OF_TASKS; j++) {
    int dailyCostPerTask = taskCostPerDay[j] * NUMBER_OF_TASKS_PER_DAY;
    int weeklyCostPerTask = dailyCost * WORK_DAYS_PER_WEEK;      
    totalCost += weeklyCostPerTask;
}
```

:::{error} Problème 
:class: dropdown
Il est beaucoup plus facile de retrouver NUMBER_OF_TASKS_PER_DAY et de le modifier que de retrouver tous les `4` dans le code. D'une façon générale,
- Utiliser des variables d'une seule lettre **SEULEMENT** pour des variables locales dans des boucles/fonctions courtes.
- Regarder le numéro de la première et de la dernière ligne de code où une variable intervient. La longueur de son nom peut croître linéairement avec le nombre de lignes de code entre les deux. 
:::


## Formatter