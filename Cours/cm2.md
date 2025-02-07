# Comment nommer les choses ?

:::{note} Disclaimer
:class: dropdown
Tous les cours à venir vous proposeront une approche structurée du concept de "code propre". Il ne s'agit pas de règles immuables, mais avant de pouvoir s'en affranchir, il est essentiel de les maîtriser. Ainsi, pour l’instant, **ces règles sont incontournables**.
:::

```{figure} ../images/searching_meaningful_variable_name.png
:alt: Nom de variable expressif
:align: center

Credits: [the_coding_love](https://thecodinglove.com/when-im-searching-for-a-meaningful-variable-name)
```

(cm2-camel-case)=
## camelCase

```{figure} ../images/camelCase.png
:alt: camelCase
:align: center

camelCase (Credits: [Wikipedia](https://fr.wikipedia.org/wiki/Camel_case))
```

:::{note} camelCase
:class: dropdown
Nous adopterons la convention **camelCase**, selon laquelle le premier mot d’une variable commence par une minuscule, tandis que chaque mot suivant débute par une majuscule.
:::

:::{seealso} snake_case
:class: dropdown
Il existe de nombreuses autres conventions de nommage, mais l’une des plus courantes après **camelCase** est **snake_case**, qui sépare les mots en minuscules à l’aide de underscores (`_`).
:::

:::{important} Variables globales et classes
1. `VARIABLE_GLOBALE` :
Les variables globales doivent utiliser la convention **UPPER_SNAKE_CASE** (aussi appelé **SCREAMING_SNAKE_CASE**). Les noms de variables globales doivent être écrites en majuscules, avec les mots séparés par des underscores (`_`). 
De manière générale, il est recommandé d'éviter l'utilisation de véritables variables globales, c'est-à-dire de variables mutables. 
Toute variable susceptible de changer de valeur devrait avoir une portée (*scope*) limitée (locale) et utiliser la convention camelCase. 
Les "variables globales" sont généralement des constantes définies avec `const <type> VARIABLE_GLOBALE`.
L'autre usage de la convention UPPER_SNAKE_CASE est pour les variables statiques et constantes à l'intérieur d'une classe.
Par exemple :
```{code} cpp
class MyClass {
private:
    static const int STATIC_CONST_VARIABLE = 100;
}
```

2. `class Object` :
Le nom d'une classe utilise **CamelCase** qui est **camelCase** mais avec une majuscule au début.
:::

(cm2-kebab-case)=
:::{important} Noms de fichiers
Nous allons utiliser le **kebab-case** (`my-first-file.txt`, `hello-world.cpp`, ...) pour les noms de fichier, car c'est la convention la plus standard pour la plupart des écosystèmes.
:::

(cm2-nom-revelateur)=
## Noms révélateurs d'intention

:::{important} Principes de base
Un nom de variable (fonction, classe, ...) doit répondre aux trois questions suivantes :
1. Pourquoi elle existe ?
2. Qu'est-ce qu'elle fait ?
3. Comment est-elle utilisée ?
:::

:::{error} Commenter un nom de variable.
:class: dropdown
Si un nom nécessite un commentaire pour être compris, c'est qu'il ne reflète pas clairement son intention.
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

Un nom de variable explicite améliore grandement la lisibilité du code. 
Prenons un exemple où nous nous contentons de renommer les variables. Que fait le code suivant ?

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

:::{hint} Indice
:class: dropdown
C'est un morceau de code du jeu **Démineur** (*Minesweeper*) dans lequel est le plateau de jeu est un `vector` qui contient des cellules représentés par des `vector<int>` où `STATUS_VALUE` est l'indice contenant le statut de la cellule et `4` correspond à la présence d'un drapeau. 

```{image} ../images/minesweeper.png
:alt: Démineur
:align: center
```
:::

:::{seealso} Pour aller plus loin.
:class: dropdown
```{code} cpp
class Cell {
private:
    const int mStatusValue = 0;
    const int mFlagged = 4;

    vector<int> values;

public:
    // Constructeur
    //...

    bool isFlagged() {
        return values[mStatusValue] == mFlagged;
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

(cm2-desinformation)=
## La désinformation

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
Même si la coloration syntaxique permet de distinguer `O` de `0` et `1` de `l`, utiliser ces caractères similaires complique inutilement la lecture du code. Cela revient simplement à rendre le code volontairement obscur...
:::

(cm2-nom-different)=
## Noms différents

```{code} cpp
void copyString(string a1, string a2, int length) {
    for (int i = 0; i < length; i++) {
        a2[i] = a1[i];
    }
}
```
:::{error} Problème 
:class: dropdown
Évitez d’ajouter des chiffres à la fin des noms de variables pour les différencier. Une alternative plus claire consiste à utiliser des noms explicites qui reflètent leur rôle distinct. Par exemple :
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
Utilisez des noms avec des différences significatives.
:::

(cm2-nom-prononcable)=
## Noms prononçables

```{code} cpp
class DtaRcrd102 {
private:
    string genymdhms;
    string modymdhms;
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
    string generationTimeStamp; // Format : yyyy-mm-dd hh:mm:ss
    string modificationTimeStamp; // Format : yyyy-mm-dd hh:mm:ss
    const string recordId = "102";
//...
};
```
:::

(cm2-nom-recherchable)=
## Noms recherchables

Imagine un code avec que des variables d'une seule lettre et des nombres fixés qui apparaissent partout et qu'il contient le morceau de code suivant.

```{code} cpp
for (int j=0; j<34; j++) {
    s += t[j]*4*5;
}
```

Maintenant, comparez-le à ce même code.

```{code} cpp
const int NUMBER_OF_TASK_TYPES = 34;
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
Il est bien plus facile de localiser et de modifier une variable nommée `NUMBER_OF_TASKS_PER_DAY` que de rechercher tous les `4` dans le code. De manière générale :
- Utilisez des variables d'une seule lettre **UNIQUEMENT** pour des variables locales dans des boucles ou des fonctions courtes.
- Considérez le numéro de la première et de la dernière ligne où une variable est utilisée. La longueur de son nom peut augmenter proportionnellement au nombre de lignes de code entre ces deux références.
:::

(cm2-class-method)=
## Nom commun pour les classes, verbe pour les méthodes

:::{note} Lisez le titre ☝️
:class: dropdown
Le nom d'une classe doit correspondre à un nom ou à un groupe nominal (par exemple, `AddressParser`). En revanche, les noms de méthodes doivent être des verbes ou des groupes verbaux (par exemple, `deleteAddress`).
:::

(cm2-un-par-concept)=
## Un seul mot par concept

:::{note} Évitez les synonymes
:class: dropdown
Il est préférable de ne pas utiliser plusieurs mots synonymes qui accomplissent la même tâche (par exemple, `fetch`, `retrieve`, `get`, ou `controller`, `manager`, `driver`). 

Certaines expressions ont des significations particulières en informatique (par exemple, `memento` fait référence à un design pattern). Il est donc important de réutiliser ces termes dans le bon contexte afin d'éviter toute ambiguïté.
:::

(cm2-contextualiser)= 
## Contextualisation

Certaines variables ont des noms suffisamment explicites pour être comprises sans contexte, mais ce n’est pas le cas de la plupart. Par exemple, des noms comme `firstName`, `lastName`, `street`, `houseNumber`, `city`, `state`, et `zipcode` suggèrent clairement qu’ils font référence aux coordonnées d’une personne. 

En revanche, tout seul, un nom comme `state` est beaucoup plus ambigu : il pourrait désigner l’état civil d’une personne, un état d’un objet dans le code, ou encore un état géographique, selon le contexte.

:::{hint} Ajouter un préfixe.
:class: dropdown
Une solution pour clarifier le contexte des noms de variables est d'ajouter un préfixe, comme `address` (si le contexte de `address` est clair), par exemple : `addressState`. 

Cependant, **il ne faut pas ajouter des préfixes de manière systématique lorsque le contexte est déjà clair**. L'objectif reste de lever toute ambiguïté, pas de rendre le code excessivement verbeux.
:::

:::{hint} Créer une classe.
:class: dropdown
Dans la plupart des cas, une solution plus efficace consiste à regrouper ces variables sous une structure commune, comme une classe : `class Address`. Cela permet de donner un contexte clair non seulement au programmeur, mais aussi à la machine.
:::

(cm2-blague)=
## (Ne pas) faire de blagues dans les noms

[`holyHandGrenade`](https://worms.fandom.com/wiki/Holy_Hand_Grenade) pour faire `deleteItems`, sérieusement ?

Bien sûr ! Tant que les bonnes pratiques sont respectées, un peu d'humour ne tue personne.