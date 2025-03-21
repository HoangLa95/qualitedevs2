# TP2 : Comment renommer les choses ?

:::{important} Git
À partir de maintenant, des rappels occasionnels vous seront donnés pour maintenir votre dépôt, mais vous devez être autonome avec Git.  
Petit rappel : voici un flux de travail typique que vous suivrez :  
- Exécutez `git remote update` et `git status` pour vérifier l’état de vos dépôts.  
- Utilisez `git pull` si votre dépôt distant a des modifications plus récentes.  
- Au moins à la fin de chaque exercice, pensez à `git add`, `git commit` et `git push` pour sauvegarder votre travail.  

N’hésitez pas à revenir au TP1 en cas de doute. La qualité de votre historique Git sera évaluée.  
:::

:::{important} Résolution de conflits
:class: dropdown
Vous n'avez pas encore appris à résoudre les conflits de fusion (branches divergentes) sur Git, qui surviennent lorsqu'il existe plusieurs versions d'un même fichier dans différents dépôts et que vous tentez de les synchroniser.
En cas de difficulté, appelez votre encadrant.
Une fois cette compétence maîtrisée, vous serez autonome dans la gestion des conflits.
:::

:::{important} Organisation des TPs et exercices
Au début du TPX, créez un dossier nommé `TPX` dans votre dépôt.  
Pour chaque exercice intitulé **"Exercice n : Nom de l'exercice"**, créez un fichier `nom-de-l-exercice.cpp` (convention **kebab-case**) et ajoutez-y le code fourni. 
:::

:::{important} Coder en anglais
Il est fortement conseillé de coder en anglais.  
Cela signifie qu'il est important de faire l'effort d'utiliser des noms en anglais lors du renommage des objets.  
Pour vous aider, tout le vocabulaire nécessaire est fourni en anglais.
:::

(tp2-objectifs)=
## Objectifs

Le but de ce TP est de comprendre les points suivants :
- [ ] [Travailler avec Git](tp1.md)
- [ ] [Magic numbers](#tp2-magic-numbers)
- [ ] [User input](#user-input)
- [ ] [Membres d'une classe C++](#tp2-membres-classe)
- [ ] [Getter et setter](#tp2-getter-setter)
- [ ] [Constructeur d'une classe](#tp2-constructor)
- [ ] [Les différentes conventions de nommage](#cm2-camel-case)
- [ ] [Noms révélateur d'intention](#cm2-nom-revelateur)

## Exercice 1 : Magic numbers

```{code} cpp
:filename: magic-numbers.cpp
#include <iostream>
#include <string>

using namespace std;

bool qualifiesForDiscount(double originalPrice) {
    return originalPrice >= 10;
}

double calculateDiscountPercentage(double originalPrice, bool isStudent, bool isLoyalCustomer) {
    double discountPercentage = 0.0;

    if (qualifiesForDiscount(originalPrice)) {
        if (isStudent) {
            discountPercentage += 10; 
        }
        if (isLoyalCustomer) {
            discountPercentage += 10;
        }
    }

    return discountPercentage;
}

double calculateDiscountAmount(double originalPrice, double discountPercentage) {
    return originalPrice * discountPercentage/100;
}

double calculateFinalPrice(double originalPrice, bool isStudent, bool isLoyalCustomer){
    double discountPercentage = calculateDiscountPercentage(originalPrice, isStudent, isLoyalCustomer);
    double discountAmount = calculateDiscountAmount(originalPrice, discountPercentage);
    double finalPrice = originalPrice - discountAmount;
    return finalPrice;
}

double redeemStudentPoints(double discountAmount){
    return discountAmount*(100-10)/100;
}

double redeemLoyaltyPoints(double discountAmount){
    return discountAmount*(100-2*10)/100;
}

double calculateRewardPoints(double discountAmount, bool isStudent, bool isLoyalCustomer){
    double rewardPoints = 0.0;
    if (isStudent){
        rewardPoints = redeemStudentPoints(discountAmount);
    } else if (isLoyalCustomer){
        rewardPoints = redeemLoyaltyPoints(discountAmount);
    }
    return rewardPoints;
}

int main() {
    const double originalPrice = 100;
    const bool isStudent = true;
    const bool isLoyalCustomer = true;

    cout << "Original price: " << originalPrice << " euros." << endl;

    double finalPrice = calculateFinalPrice(originalPrice,isStudent,isLoyalCustomer);
    cout << "Final price with student and loyalty discounts: " << finalPrice << " euros." << endl;

    double discountAmount = originalPrice - finalPrice;
    cout << "Your reward points: " << calculateRewardPoints(discountAmount,isStudent,isLoyalCustomer) << endl;

    return 0;
}
```

1. Enregistrez puis compilez le code (avec `g++ -o magic-numbers magic-numbers.cpp`). 

:::{important} Nom de l'exécutable
Le premier `<nom du fichier>` dans la commande de compilation correspond au nom de l'exécutable.  
Dans ce cours, nous utiliserons toujours le même nom que le fichier `.cpp` pour l'exécutable.
:::

2. Exécutez `./magic-numbers`.

3. Comprenez-vous ce que fait ce code ?

:::{hint} Indices
:class: dropdown
Un client achète un produit au prix de `originalPrice`.  
Il peut bénéficier de réductions s'il est étudiant ou client fidèle, et les réductions obtenues permettent également de calculer des points de récompense.  

Les règles sont les suivantes :  

- Une réduction ne s'applique que si `originalPrice` dépasse 10 euros.  
- Un étudiant bénéficie d'une réduction de 10 %.  
- Un client fidèle bénéficie également d'une réduction de 10 %.  
- Si le client est à la fois étudiant et fidèle, les réductions s'additionnent pour atteindre 20 %.  
- Avec `originalPrice = 100` euros, la réduction totale est donc de 20 euros.  
- Les points de récompense sont calculés en appliquant un pourcentage à cette réduction :  
    - $\frac{100 - \text{réduction étudiant}}{100}$ (soit 90 % ici) donne 18 points.  
    - $\frac{100 - 2 \times \text{réduction client fidèle}}{100}$ (soit 80 % ici) donne 16 points.  
- La réduction étudiante étant toujours plus avantageuse, elle est appliquée en priorité.  
- Ainsi, dans cet exemple, le client reçoit **18 points**. 
:::

4. Bien que les noms des éléments soient appropriés, ce code n'est pas propre. En voyez-vous les raisons ?

(tp2-magic-numbers)=
:::{hint} Magic numbers
:class: dropdown
On appelle *magic numbers* des nombres utilisés directement dans le code.
Souvent, ces nombres apparaissent sans justification claire. 
Dans cet exemple, bien que la signification de certains nombres soit déductible grâce aux autres éléments bien nommés, il est préférable de les éviter. 

Certains nombres magiques peuvent être acceptables, comme `100` pour les pourcentages, `0` pour initialiser une variable ou `1` pour un incrément.
Toutefois, la majorité d'entre eux devrait être évitée.

Ici, le nombre `10` apparaît plusieurs fois, mais sa signification varie selon le contexte. Imaginons que nous voulions modifier le seuil d'éligibilité pour une réduction, ajuster les pourcentages de réduction ou modifier les formules de récompenses. Le risque d'erreur devient alors considérable. De plus, il est difficile de repérer et de modifier les bonnes valeurs sans devoir replonger dans l'ensemble du code pour en comprendre la logique.

La solution consiste à attribuer des noms explicites à ces nombres magiques en fonction de leur portée, qu'ils soient locaux ou globaux. 
Il est essentiel d'adopter les bonnes conventions de nommage abordées en cours afin d'éviter les nombres magiques.
:::

5. Nommez les magic numbers.

:::{hint} Avez-vous accompli votre tâche de manière satisfaisante ?
:class: dropdown
Une façon simple de vérifier si vous avez correctement appliqué les conventions de nommage est d'essayer de modifier un seul paramètre à la fois (par exemple, le pourcentage de réduction étudiant, réduction client, etc.).

- Auriez-vous pu faire cette modification dans un code similaire de 10 000 lignes en utilisant une recherche ({kbd}`Ctrl` + {kbd}`F`) ?
- Avez-vous réussi à modifier une seule variable (une seule ligne dans le code) ?

Dans le cas contraire, renommez-les.
:::

### User input

Pour éviter de recompiler le code à chaque fois que vous modifiez `originalPrice`, `isStudent` et `isLoyalCustomer`, nous pouvons récupérer les valeurs par clavier lors de l'exécution.

6. Remplacez les lignes suivantes par le code qui suit.

```{code} cpp
const double originalPrice = 100;
const bool isStudent = true;
const bool isLoyalCustomer = true;
```

```{code} cpp
double userOriginalPrice;
cout << "Enter the original price: ";
cin >> userOriginalPrice;
const double& originalPrice = userOriginalPrice;

bool userIsStudent;
cout << "Are you a student? (1:Yes, 0:No) ";
cin >> userIsStudent;
const bool& isStudent = userIsStudent;

bool userIsLoyalCustomer;
cout << "Do you have a loyalty card? (1:Yes, 0:No) ";
cin >> userIsLoyalCustomer;
const bool& isLoyalCustomer = userIsLoyalCustomer;
```

:::{note} `const`
:class: dropdown
L'utilisation de `const <type>&` après les entrées utilisateur permet de garantir que ces valeurs (qui restent constantes tout le long de l'exécution) ne pourront pas être modifiées accidentellement dans le code, ce qui évite des erreurs imprévues.
La référence (`&`) évite de faire des copies des valeurs de variables, ceci est surtout important quand nous travaillons avec des objets qui prennent beaucoup de mémoire.
Ici, l'utilisation d'une référence n'est pas nécessaire, car les types `double` et `bool` sont peu gourmands en mémoire. C'est principalement pour vous montrer un exemple d'usage de `&`.

De façon générale, l'usage de `const` devant les variables immutables permet de rendre le code plus clair en indiquant explicitement que ces valeurs ne doivent pas être modifiées, ce qui facilite la maintenance et la compréhension du programme.
**Avez-vous bien utilisé les `const` dans votre code ?**
:::

7. Testez votre code et répondez aux questions du **Quiz**.

:::{important} Git
:class: dropdown
Avez-vous pensé à maintenir vos dépôts ?
:::


## Exercice 2 : Classes et méthodes

```{code} cpp
:filename: classes-et-methodes.cpp
#include <iostream>
using namespace std;

double b1 = 0.0;
double b2 = 100.0;

void d1(double x) {
    b1 += x;
}

void d2(double x) {
    b1 += x;
}

void w1(double x) {
    if (x + 4 <= b1) {
        b1 -= (x + 4);
    } else {
        cout << "Insufficient funds!" << endl;
    }
}

void w2(double x) {
    if (x + 2 <= b2) {
        b2 -= (x + 2);
    } else {
        cout << "Insufficient funds!" << endl;
    }
}

int main() {
    d1(100.0);
    w1(50.0);
    w2(50.0);
    cout << "First account balance: " << b1 << endl;
    cout << "Second account balance: " << b2 << endl;
    return 0;
}
```
1. Comprenez-vous ce que fait ce code ?

:::{hint} Indices
:class: dropdown
- Il existe deux comptes (*account*) avec deux types différents : le type `1` que nous allons appeler "Basic" et le type `2`, appelé "Standard".
- Le premier compte `b1` possède un solde (*balance*) initial de `0.0` euro, tandis que le deuxième compte `b2` a un solde initial de `100.0` euros.
- Il est possible de déposer (*deposit*) une somme (*amount*) `x` sur chacun des comptes, respectivement via `d1` pour le premier compte et `d2` pour le second.
- L'argent peut être retiré (*withdraw*) avec `w1` pour le premier compte et `w2` pour le second.
- Lors d'un retrait, le compte de type `1` (Basic) applique un frais de retrait (*withdrawal fee*) de 4 euros, tandis que le compte de type `2` (Standard) applique un frais de 2 euros.
:::

2. **Quiz** : Lequel des deux comptes dispose du solde le plus élevé après ces transactions ?

3. Remplacez les transactions précédentes par les suivantes :

```{code} cpp
d1(100.0);
w1(50.0);
d2(100.0);
w2(50.0);
```

4. **Quiz** : Maintenant, quel est le compte qui dispose du solde le plus élevé ?

:::{warning} Avez-vous repéré l'erreur ?
:class: dropdown
- Le code est-il logique ? 
- Quelle est l'origine de l'erreur comptable ? 
- Est-il facile d'ajouter et de réaliser des transactions avec un troisième compte ?
- Quelles améliorations peuvent être apportées à ce code ?
:::

5. Pour améliorer ce code, nous allons le réécrire de la manière suivante :

(tp2-ex2-class-example)=
```{code} cpp
:filename: classes-et-methodes.cpp
#include <iostream>
using namespace std;

enum t {B, S};

class b {
private:
    double mB1;
    t mT;
    double mF;

public:
    b(double b1, t t1) : mB1(b1), mT(t1) {
        switch(t1){
            case B:
                mF = 4;
                break;
            case S: 
                mF = 2;
                break;
        }
    }

    void d(double x) {
        mB1 += x;
    }

    void w(double x) {
        if (x + mF <= mB1) {
            mB1 -= (x + mF);
        } else {
            cout << "Insufficient funds!" << endl;
        }
    }

    double getB1() const {
        return mB1;
    }

    string tToString() const {
        switch(mT){
            case B: return "Basic";
            case S: return "Standard";
            default: return "Unknown";
        }
    }
};

int main() {
    b a1(0.0,B);
    b a2(100.0,S);
    a1.d(100.0);
    a1.w(50.0);
    a2.d(100.0);
    a2.w(50.0);
    cout << "First account type: " << a1.tToString() << " and balance: " << a1.getB1() << endl;
    cout << "Second account type: " << a2.tToString() << " and balance: " << a2.getB1() << endl;
    return 0;
}
```
(tp2-membres-classe)=
:::{important} Membres d'une classe C++
Les membres `private` d'une classe en C++ sont fréquemment préfixés par `m` (par exemple, `mB1`, `mT` et `mF`). Ce préfixe permet de les différencier des variables locales utilisées dans le constructeur (par exemple, `double b1`), qui servent à initialiser les membres comme `mB1` (avec `mB1(b1)`).
Il existe d'autres conventions, comme l'utilisation du préfixe `_` (par exemple, `_b1`).

Cependant, cette pratique n'est pas systématique. Par exemple, il est possible de faire du *shadowing*, où une variable locale et un membre de la classe partagent le même nom. Dans ce cas, la distinction se fait en utilisant `this->variable` pour désigner l'attribut de la classe, et simplement `variable` pour la variable locale.
En Java, le shadowing est la convention la plus utilisée (plus de détails à ce sujet dans votre cours de Développement Orienté Objet).

Dans ce cours, nous adopterons la convention du préfixe (soit `m`, soit `_`) afin de clarifier la distinction entre les membres de la classe et les autres variables.
Il faut rester cohérent sur tout le dépôt, **il ne faut pas mélanger `m` et `_`**.
:::

(tp2-getter-setter)=
:::{important} Getter et Setter
L'**encapsulation** est essentielle pour protéger les données sensibles de votre objet. 
Il est donc important de déclarer les attributs de votre classe en tant que `private`. 
En conséquence, il est nécessaire d'utiliser des méthodes `get` (pour accéder) et `set` (pour modifier) ces attributs.
Ces méthodes ne doivent pas être définies systématiquement. Définissez uniquement les méthodes `get` pour les attributs auxquels nous devons accéder, et les méthodes `set` pour les attributs que nous autorisons à modifier depuis l'extérieur de la classe.

La convention recommandée pour le nommage de ces méthodes est `getNomAttribut` et `setNomAttribut` (sans le préfixe `m`).
:::

:::{note} Méthode `const` dans une classe
:class: dropdown
Une méthode const est une fonction membre d'une classe qui, par le mot-clé const placé à la fin de sa déclaration, garantit qu'elle ne modifie pas les attributs de l'objet. Cela s'applique notamment aux getters, qui sont souvent définis de la manière suivante :

```{code} cpp
<attributeType> getAttribute() const {
    return mAttribute;
}
``` 
:::

6. Comprenez-vous ce que fait ce code ?

(tp2-constructor)=
:::{hint} Indices
:class: dropdown
- L'objet `t` de type `enum` peut prendre les valeurs `B` (pour "Basic") ou `S` (pour "Standard").
- La classe `b` représente un compte bancaire (*bank account*).
- Elle possède les attributs suivants :
    - `mB1`, qui représente son solde (*balance*).
    - `mT`, qui indique son type (Basic ou Standard).
    - `mF`, qui est son frais de retrait (*withdrawal fee*).

Le code suivant est le constructeur de la classe :
```{code} cpp
b(double b1, t t1) : mB1(b1), mT(t1) {
    switch(t1){
        case B:
            mF = 4;
            break;
        case S: 
            mF = 2;
            break;
    }
}
```

- Un objet de la classe `b` est construit avec deux arguments : `double b1`, qui est son solde initial, et `t t1`, qui définit le type du compte (*account type*).
- Le `switch(t1)` permet de gérer les différents types de comptes.
    - Si `t1 == B`, le frais de retrait est de 4 euros.
    - Si `t1 == S`, le frais de retrait est de 2 euros.
- La méthode `tToString()` permet d'afficher les types sous forme de chaînes de caractères correspondantes.
:::

7. **Quiz** : Cette fois, quel est le compte qui dispose du solde le plus élevé ?

8. Ce code compile et fonctionne comme il se doit, mais il est une véritable atrocité pour les yeux. Il me fait mal au cœur. Il est impératif de renommer ces horreurs.

:::{hint} Avez-vous accompli votre tâche ?
:class: dropdown
- Avez-vous correctement appliqué la convention **camelCase** ?
- Avez-vous respecté les conventions de nommage pour les classes et les méthodes ? (Les noms des objets `enum` doivent également commencer par une majuscule.)
- Avez-vous suivi les conventions concernant l'utilisation du préfixe `m` (ou `_`) et des getters et setters ?
- Y a-t-il encore des variables, méthodes ou objets dont le nom se résume à une seule lettre ?
- Des nombres sans signification figurent-ils encore dans les noms ?
- Les noms que vous avez choisis sont-ils suffisamment révélateurs de l'intention derrière chaque variable, méthode ou objet ?

Si vous avez des doutes, n'hésitez pas à revenir sur le [cours](../Cours/cm2.md).

Si vous pensez avoir correctement renommé les éléments, rappelez-vous qu'un bon programmeur devrait être capable d'écrire ce même code correctement dès le départ. Bien sûr, il peut toujours revenir sur son code pour l'améliorer, mais le niveau de code présenté dans cet exercice est tout simplement inacceptable. 
À la fin de ce cours, vous devez être capable de bien nommer les éléments dès le départ.
D'ailleurs, j'ai passé plus de temps à rendre ce code fonctionnel, mais visuellement désastreux, qu'à l'écrire correctement dès le début.
Des noms incohérents et flous rendent le code difficile à comprendre et remettent en question sa logique.

J'espère que cet exercice vous a convaincu de l'importance de coder de manière propre 😊.
:::

9. Recompilez et testez votre code.

10. Ajoutez un troisième type "Premium" avec un frais de retrait de 1 euro.

11. Dans la fonction `int main()`, remplacez le code existant par les instructions suivantes :
- Créez trois comptes avec les trois types différents (Basic, Standard et Premium), chacun ayant un solde initial de 100,0 euros.
- Effectuez un dépôt de 50 euros sur chaque compte.
- Effectuez un retrait de 148 euros de chaque compte.
- Affichez les informations (types et soldes) des trois comptes.

12. **Quiz** : Quelle est la somme des soldes des trois comptes ?

Revenez aux [objectifs](#tp2-objectifs) et cochez les points que vous avez maîtrisés. Revenez sur les points que vous n'avez pas encore bien compris. Appelez votre encadrant si besoin.

:::{important} Git
:class: dropdown
À la fin de ce TP, votre dépôt distant devrait être structuré comme suit :
- À la racine, vous trouverez les dossiers `TP1/` et `TP2/`, ainsi que les fichiers `.gitignore` et `README.md`. Vous pouvez aussi voir le dossier de configuration `.git` si vous affichez les dossiers cachés.
- Le dossier `TP1/` doit contenir les fichiers `hello-world.cpp` et `my-first-file.txt`.
- Le dossier `TP2/` doit contenir les fichiers `classes-et-methodes.cpp` et `magic-numbers.cpp`

Votre répertoire de travail peut contenir des fichiers ignorés, mais veillez à vérifier que votre dépôt sur GitLab est propre, car c'est lui qui sera évalué à la fin du cours !

Si ce n'est pas le cas, retournez à [la fin du TP1](#tp1-pour-finir), où des instructions supplémentaires ont été ajoutées pour vous aider à nettoyer votre dépôt distant.
:::
