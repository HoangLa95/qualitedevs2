# TP2 : Comment renommer les choses ?

:::{warning} Utilisation d'un IDE
:class: dropdown
Pour vous familiariser avec les commandes Git sous Linux, nous allons apprendre à utiliser un IDE (comme VSCodium sur la version Debian de l'IUT) plus tard. Pour le moment, vous pouvez utiliser n'importe quel éditeur de texte de base (sans configuration, autocomplétion,...). Nous passerons à l'IDE quand les intervenants trouvent que vous avez bien compris comment fonctionne le workflow de git (avec pull, add, commit, push).
:::

:::{important} QCM
Les réponses aux questions du TPs sont à remplir dans le QCM sur Moodle.
:::

**Question 1** : Par quoi faut-il commencer quand vous ouvrez votre dépôt local ?

Créer un dossier `TP2` dans votre dépôt. Pour chaque exercice "Exercice n : Nom de l'exercice", vous allez créer un fichier `nom-de-l-exercice.cpp`. 

## Exercice 1 : Magic numbers

Recopier le code suivant dans votre fichier `magic-numbers.cpp`.

```{code} cpp
#include <iostream>
using namespace std;

bool b(int a, float m) {
    if (a >= 18 && m >= 7) {
        return true;
    }
    return false;
}

int main() {
    int a = 19;
    float m = 10.0;
    if (b(a, m)) {
        cout << "You can buy beer!" << endl;
    } else {
        cout << "Sorry, you cannot buy beer." << endl;
    }
    return 0;
}
```

Enregistrer et compiler le code avec `g++ -o <nom du fichier> <nom du fichier>.cpp` (par exemple `g++ -o magic-numbers magic-numbers.cpp`). 

:::{note} Nom de l'exécutable
Le premier `<nom du fichier>` dans la commande de compilation correspond au nom de l'exécutable. Dans ce cours, nous allons adopter la convention suivante : les exécutables n'auront pas d'extension. Nous reprenons aussi le nom du fichier `.cpp` par commodité.
:::

Maintenant, exécuter `./magic-numbers`.

**Question 2** : Quel message s'affiche dans le terminal ?

Pouvez-vous deviner ce que fait ce code ? Comment pourrait-on améliorer les noms des variables ? Pour vous aider, essayer de renommer les variables pour répondre aux questions suivantes.
- Que fait la fonction `b` ?
- À quoi correspond `int a` (*indice* : l'âge légal de consommation d'alcool en France est 18) ?
- À quoi correspond `float m` (*indice* : le prix moyen d'une bouteille de bière de 500 ml est 7 euros dans les restaurants à Paris) ?
- À quoi correspond `18` et `7` (*indice* : questions précédentes) ?

**Question 3** : Qu'avez-vous renommé ?

Enregistrer votre nouveau code et recompiler. Exécuter-le pour vérifier que tout fonctionne bien. Vous pouvez aussi changer les valeurs données (`19` et `10.0`) pour tester votre code.

(tp2-magic-numbers)=
:::{note} Magic numbers
:class: dropdown
On appelle *magic numbers* des nombres qui apparaissent dans le code sans aucun contexte et/ou explication. Éviter les magic numbers en leur donnant des noms révélateurs d'intention. Ces nombres peuvent être utilisés localement ou bien être des variables globales, c'est à vous de décider quel est le *scope* de ces variables et donc d'utiliser les bonnes conventions de nommage.
:::

:::{important} .gitignore
:class: dropdown
Nous ne voulons pas suivre l'exécutable donc rajouter la ligne suivante au fichier `.gitignore` : `/TP2/magic-numbers`.
:::

:::{important} git add, commit, push
:class: dropdown
Il est temps de suivre (`add`) les changements, les sauvegarder (`commit -m`) avec un message explicite et les diffuser (`push`) sur le dépôt distant (sur le GitLab de l'IUT). Vérifier que le changement a bien été effectué sur l'application web GitLab.
:::

### Simplification du code

Quand vous avez un code de la forme suivante :

```{code} cpp
if (someCondition) {
    return true;
}
return false;
```
ou
```{code} cpp
if (someCondition) {
    return true;
} else {
    return false;
}
```
Il suffit de remplacer ce code par :
```{code} cpp
return someCondition;
```
Simplement, si la condition est vraie, alors on retourne `true` et si elle est fausse, on retourne `false`. `if (veriteDeLaCondition){return true;} else {return false;}` est redondant quand on peut simplement faire `return veriteDeLaCondition`.

Simplifier le code précédent avec ce que vous venez de voir.

:::{note} Rappel Git
:class: dropdown
Il est à nouveau temps de suivre (`add`) les nouveaux changements, les sauvegarder avec un message (`commit -m`), puis de les diffuser (`push`) sur le dépôt distant.
:::

### User input

Pour éviter de recompiler le code à chaque fois que vous modifier les variables `a` et `m`, nous pouvons demander à l'utilisateur de rentrer les valeurs lors de l'exécution. Par exemple :

```{code} cpp
int main() {
    int a;
    float m;

    cout << "Enter your age: ";
    cin >> a;

    cout << "Enter the amount of money you have: ";
    cin >> m;

    if (b(a, m)) {
        cout << "You can buy beer!" << endl;
    } else {
        cout << "Sorry, you cannot buy beer." << endl;
    }
    return 0;
}
```

Modifier votre code pour permettre du *user input*. Recompiler et tester votre code.

:::{important} Synchroniser les dépôts
:class: dropdown
N'oublier pas de synchroniser (`add`, `commit`, `push`) vos dépôts locaux et distants de temps en temps (après chaque exercice par exemple).
:::


## Exercice 2 : Classes et Méthodes

Recopier le code suivant.

```{code} cpp
#include <iostream>
using namespace std;

float account_balance1 = 0.0;
float account_balance2 = 100.0;

void deposit1(float x) {
    account_balance1 += x;
}

void deposit2(float x) {
    account_balance1 += x;
}

void withdraw1(float x) {
    if (x <= account_balance1) {
        account_balance1 -= x;
    } else {
        cout << "Insufficient funds!" << endl;
    }
}

void withdraw2(float x) {
    if (x <= account_balance2) {
        account_balance2 -= x;
    } else {
        cout << "Insufficient funds!" << endl;
    }
}

float get1() {
    return account_balance1;
}

float get2() {
    return account_balance2;
}

int main() {
    deposit1(100.0);
    withdraw1(50.0);
    withdraw2(50.0);
    cout << "First Account Balance: " << get1() << endl;
    cout << "Second Account Balance: " << get2() << endl;
    return 0;
}
```

**Question 4** : Quel est le compte avec le plus d'argent après ces transactions ?

Remplacer les anciennes transactions avec les suivantes :

```{code} cpp
deposit1(100.0);
withdraw1(50.0);
deposit2(100.0);
withdraw2(50.0);
```

**Question 5** : Maintenant, quel est le compte avec le plus d'argent ?

- Est-ce que ce code a du sens ? 
- D'où vient l'erreur de comptabilité ? 
- Est-ce que je peux facilement créer et faire des transactions avec un troisième compte ?
- Que faut-il faire pour améliorer ce code (*indice* : nom de l'exercice) ?

Pour améliorer le code, nous allons créer une classe `bank_account` de la façon suivante :

(tp2-ex2-class-example)=
```{code} cpp
#include <iostream>
using namespace std;

class bank_account {
public:
    bank_account(float b) : balance(b) {}

    void ba_deposit(float x) {
        balance += x;
    }

    void ba_withdraw(float x) {
        if (x <= balance) {
            balance -= x;
        } else {
            cout << "Insufficient funds!" << endl;
        }
    }

    float ba_get() {
        return balance;
    }

private:
    float balance;
};

int main() {
    bank_account first_account(0.0);
    bank_account second_account(100.0);
    first_account.ba_deposit(100.0);
    first_account.ba_withdraw(50.0);
    second_account.ba_deposit(100.0);
    second_account.ba_withdraw(50.0);
    cout << "First Account Balance: " << first_account.ba_get() << endl;
    cout << "Second Account Balance: " << second_account.ba_get() << endl;
    return 0;
}
```

**Question 6** : Maintenant, quel est le compte avec le plus d'argent ?

Ce code compile et marche comme il faut. Par contre, il me fait mal au coeur. Renommer cette atrocité. 

:::{note} Membres d'une classe C++
:class: dropdown
Les `private` class member en C++ sont souvent préfixés avec `m` (par exemple `mBalance`). Le but de ce préfixe est de le différencier avec la variable locale utilisé dans le constructeur (ici `float b` que l'on pourrait renommer `float balance`) que l'on utilise pour initialiser `mBalance` (avec `mBalance(balance)` dans ce cas).

Il existe aussi d'autres conventions comme commencer par `_` (par exemple `_balance`). L'idée d'utiliser la lettre `m` est qu'il existe aussi d'autres lettres avec d'autres significations et ces lettres peuvent être utilisées pour préfixer les noms des variables. Cette convention vient de la Notation Hongroise qui est devenu maintenant assez obsolète mais il y a des restes de cette notation qui sont encore utiles (par exemple l'utilisation de `m` ou pour prendre un autre exemple : `s` pour les variables statiques).

Nous pourrions aussi renommer `float b` avec `float initialBalance` et garder `balance` comme nom d'attribut.

Cette convention n'est pas forcément utilisée dans d'autres langages. Par exemple, dans Java et Python, on peut faire du *shadowing* où la variable locale et le membre de la classe peut avoir le même nom et la différence entre ces deux variables se font grâce à `this` (plus d'informations dans votre cours de programmation objet).
:::

:::{note} Getter et Setter
:class: dropdown
L'**encapsulation** est importante pour cacher les données *sensitives* de votre objet. Il est donc important de déclarer les attributs de votre classe comme `private`. Conséquemment, il faut utiliser des méthodes `get` (pour récupérer) et `set` (pour modifier) ces attributs.

La convention utilisée pour les noms de ces méthodes sont `getNomAttribut` et `setNomAttribut`.
:::

Un checklist pour vous aider (revenir sur le cours si vous ne comprenez pas certains principes) :
- [ ] [camelCase](#cm2-camel-case)
- [ ] [Majuscule pour les noms de classes](#cm2-class-method)
- [ ] [Nom révélateur d'intention](#cm2-nom-revelateur)
- [ ] [Contexte](#cm2-contextualiser) (sauf quand inutile)

**Question 7** : Qu'avez-vous renommé ?

Recompiler votre code et vérifier que le code fonctionne bien en créant un troisième compte avec une montant initial de `123.45`, puis déposer `67.89` dans le compte et enfin retirer `101.1`.

**Question 8** : Combien d'argent se trouve dans le troisième compte ?

:::{important} `.gitignore`, `add`, `commit`, `push`
:class: dropdown
Ne pas oublier d'ignorer l'exécutable puis de mettre à jour votre dépôt distant.
:::

## Exercice 3 : Contexte

Regarder le code suivant.

```{code} cpp
#include <iostream>
#include <string>

using namespace std;

void printGrammaticallyCorrectMessage(string candidate, int count) {
    string number;
    string verb;
    string pluralModifier;
    
    if (count == 0) {
        number = "no";
        verb = "are";
        pluralModifier = "s";
    } else if (count == 1) {
        number = "1";
        verb = "is";
        pluralModifier = "";
    } else {
        number = to_string(count);
        verb = "are";
        pluralModifier = "s";
    }
    
    string message = "There " + verb + " " + number + " " + candidate + pluralModifier + ".";
    cout << message << endl;
}

int main() {
    printGrammaticallyCorrectMessage("bicycle", 0);
    printGrammaticallyCorrectMessage("house", 1);
    printGrammaticallyCorrectMessage("computer", 2);
    return 0;
}
```

Essayer d'abord de comprendre ce que fait ce code. Tout a l'air d'être bien nommé, quel est donc le problème de ce code ?

:::{hint} Contexte
:class: dropdown
Le nom de la fonction donne une partie du contexte des variables. Le reste du contexte doit être déduit de l'algorithme : par exemple, on se rend compte que `number`, `verb`, `pluralModifier` font partie d'un message que l'on va afficher et ces variables vont dépendre du nombre `count` d'objets `candidate`.

À première vue, le contexte, la signification des variables et ce que fait la fonction reste assez opaque.
:::

- Contextualiser ce code en rajoutant une classe `GrammaticallyCorrectMessage`.
- Dans `private:` 
    - Les variables `number`, `verb` et `pluralModifier` doivent être des attributs privés de `GrammaticallyCorrectMessage`. Il n'y aura pas d'affectation directe des attributs ici donc vous pouvez garder les mêmes noms (sans préfixer par `m`).
    - Une fonction devrait faire seulement une chose. Séparer donc les différents cas en différentes fonctions (par exemple, `thereAreNoCandidates`, `thereIsOneCandidate`, `thereAreMultipleCandidates`) qui affectent différentes valeurs aux attributs.
    - Maintenant, nous pouvons créer une fonction `assignValuesToMessageParts` qui appelle les fonctions correspondantes selon la valeur de `count`.
- Dans `public:`
    - Nous pouvons maintenant créer une fonction `createMessage` qui prend `string candidate` et `int count` en arguments et qui appelle `assignValuesToMessageParts` puis retourne le message avec la bonne formulation (sous forme de `string`).
- Dans `main`
    - Nous pouvons maintenant créer un message `GrammaticallyCorrectMessage message`.
    - Il suffit d'appeler la méthode `createMessage` avec les bons arguments et de l'afficher dans le terminal avec `cout <<`. N'oublier pas d'aller à ligne avec `<< endl`.

**Question 9** : Combien d'attributs et de méthodes se trouvent dans `GrammaticallyCorrectMessage` ?

**Question 10** : Quels sont les types que `assignValuesToMessageParts` et `createMessage` vont retourner ?

Compiler et tester votre code. 
- Maintenant, l'intention de ce code est très explicite même sans commentaire.
- Les variables sont bien situées dans leur contexte pour celui qui lit le code aussi bien que pour la machine qui le compile grâce à l'encapsulation à l'intérieur de la classe `GrammaticallyCorrectMessage`. 
- Le code est bien modulable car chaque fonction ne fait qu'une seule chose. La modification d'une fonction ne changera pas le comportement des autres fonctions. 
- Il est aussi facile de changer le code pour modifier la structure de la phrase.

:::{note} Rappel
:class: dropdown
N'oubliez pas de maintenir votre dépôt Git ! (Ajouter l'exécutable à `.gitignore`, `add`, `commit`, `push`)
:::
