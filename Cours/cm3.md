# Comment écrire une fonction ?

```{figure} ../images/small-functions.jpg
:alt: Les fonctions courtes
:align: center

Credits: [Memegenerator](https://imgflip.com/memegenerator)
```

:::{important} La première règle du function club...
:class: dropdown
- La première règle du function club est qu'une fonction doit être courte. 
- La deuxième règle du function club est qu'une fonction doit être encore plus courte.
:::

:::{note} Pourquoi une fonction courte ?
:class: dropdown
Une fonction courte est plus facile à lire et à comprendre. Plus elle est concise, plus il est simple d'en saisir la structure et de repérer d'éventuelles erreurs.
:::

**Qu'est-ce qu'une fonction courte ?**

Voici une fonction longue :

```{code} cpp
#include <cmath>
#include <vector>

using namespace std;

// Generates a list of prime numbers up to maxValue 
// using the Sieve of Eratosthenes
vector<int> generatePrimes(int maxValue) {
    // Prime numbers are only possible starting from 2
    if (maxValue < 2) {
        return vector<int>();
    }

    int rangeSize = maxValue + 1;
    vector<bool> isPrime(rangeSize, true);
    isPrime[0] = isPrime[1] = false;

    // Mark non-prime numbers using the Sieve of Eratosthenes
    // Any non-prime number n must have at least one factor <= sqrt(n)
    for (int currentNumber = 2; currentNumber <= sqrt(rangeSize); currentNumber++) {
        if (isPrime[currentNumber]) {
            for (int multiple = 2 * currentNumber; multiple < rangeSize; multiple += currentNumber)
                isPrime[multiple] = false;
        }
    }

    // Store all prime numbers in a new vector
    vector<int> primes;
    for (int number = 0; number < rangeSize; number++) {
        if (isPrime[number])
            primes.push_back(number);
    }

    return primes;
}
```

Que fait cette fonction ?
```{figure} ../images/sieve-animation.gif
:alt: Crible d'Érastosthènes
:align: center

Crible d'Ératosthènes (Credits: [Wikipedia](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes))
```

## Comment écrire une fonction courte ?

### Petites boucles et peu d'indentations

:::{important} Longueur d'un bloc
:class: dropdown
Les blocs de code à l’intérieur d’une boucle doivent rester courts.
Si un bloc devient trop long, il peut être refactorisé en une fonction séparée avec un nom descriptif.
:::

:::{important} Nombre d'intentations
:class: dropdown
Plus le code contient de structures imbriquées, plus il devient difficile à comprendre.
:::

### Responsabilité unique

(cm3-unique-responsibility)=
:::{important} Principe de responsabilité unique
:class: dropdown
Une fonction doit accomplir une seule tâche, et cette responsabilité doit être clairement indiquée dans son nom et/ou sa documentation / ses commentaires.

Si une fonction contient plusieurs sections (qui ne sont pas des appels à d’autres fonctions), cela signifie qu’elle accomplit trop de choses.
:::

### Un seul niveau d'abstraction

:::{warning} Ne pas mélanger les niveaux
:class: dropdown
Une fonction comportant plusieurs niveaux d’abstraction est difficile à comprendre.
Dans l’exemple précédent, on mélange deux niveaux d’abstraction tels que :
- *Un nombre non premier n doit avoir un facteur inférieur ou égal à la racine carrée de n*, et
- *Ajouter les nombres premiers dans un nouveau vecteur*.
:::

(cm3-stepdown-rule)=
:::{important} *The Stepdown Rule*
Une fonction doit se lire comme un livre avec un sommaire bien structuré.
- Au plus haut niveau, on trouve les différents chapitres.
- À l’intérieur d’un chapitre, on trouve les différentes sections.
- À l’intérieur d’une section, on trouve du contenu qui traite d’une seule et même chose.

Chaque niveau d’abstraction correspondrait ainsi à une fonction.
:::

Maintenant, appliquons nos principes au code précédent.

```{code} cpp
#include <cmath>
#include <vector>

using namespace std;

// Generates a list of prime numbers up to maxValue 
// using the Sieve of Eratosthenes
vector<int> generatePrimes(int maxValue) {
    // Prime numbers are only possible starting from 2
    if (maxValue < 2) {
        return vector<int>();
    }

    int rangeSize = maxValue + 1;
    vector<bool> isPrime(rangeSize, true);
    isPrime[0] = isPrime[1] = false;
    
    markNonPrimes(isPrime);

    return collectPrimes(isPrime);
}

// Marks non-prime numbers using the Sieve of Eratosthenes
void markNonPrimes(vector<bool>& isPrime) {
    // Any non-prime number n must have at least one factor <= sqrt(n)
    for (int currentNumber = 2; currentNumber <= sqrt(isPrime.size()); currentNumber++) {
        if (isPrime[currentNumber]) {
            markMultiplesAsNonPrime(isPrime, currentNumber);
        }
    }
}

void markMultiplesAsNonPrime(vector<bool>& isPrime, int prime) {
    for (int multiple = 2 * prime; multiple < isPrime.size(); multiple += prime) {
        isPrime[multiple] = false;
    }
}

vector<int> collectPrimes(const vector<bool>& isPrime) {
    vector<int> primes;
    for (int number = 0; number < isPrime.size(); number++) {
        if (isPrime[number])
            primes.push_back(number);
    }
    return primes;
}
```

## Comment écrire une fonction simple ?

### Pas de duplications de code

:::{important} Refactoriser !
:class: dropdown
La duplication de code comporte un risque d'erreur : chaque modification de la logique nécessite de répercuter le changement à plusieurs endroits, de manière cohérente.

Dans ce cas, il est nécessaire de refactoriser le code.
:::

(cm3-peu-arguments)=
### Peu d'arguments

:::{hint} Le nombre idéal d'arguments d'une fonction
:class: dropdown
0 !
:::

```{code} cpp
bool isEven(int number);

double squareRoot(int number);

void printMessage(const string& message);
void removeVowels(string& inputString);
```

:::{important} Un seul argument
Il existe trois types de fonctions prenant un seul argument :
1. **Fonction booléenne** : elle vérifie une condition en fonction de l'argument et retourne un booléen.
2. **Fonction de transformation** : elle prend un argument en entrée et retourne une valeur, éventuellement d'un autre type.
3. **Fonction d'action** : elle déclenche un événement sans retourner de valeur (*void*).

Il faut éviter les fonctions à un argument qui ne sont pas dans une de ces catégories.
:::

```{code} cpp
void switchPower(bool onOrOff);
```

:::{warning} *Flag argument*
:class: dropdown
Si une fonction prend un booléen en argument, il est important de s'assurer qu'elle ne réalise pas deux actions distinctes : une lorsque le booléen est vrai, et une autre lorsqu'il est faux.

Dans ce cas, il est préférable de séparer la fonction en deux. Par exemple :
```cpp
void turnPowerOn();
void turnPowerOff();
```
Si les comportements des deux fonctions sont très similaires, la séparation peut entraîner une duplication de code. Dans ce cas, l'utilisation d'un flag peut être justifiée, mais il faut être prudent !
:::

```{code} cpp
Point createPoint(int xCoordinate, int yCoordinate);
Matrix multiply(const Matrix& leftMatrix, const Matrix& rightMatrix);
bool verifyBehavior(const SomeType& expected, const SomeType& actual);
```

:::{warning} Deux arguments
L'utilisation de fonctions à deux arguments peut être risquée, sauf lorsque les deux arguments ont un ordre naturel ou lorsqu'ils sont clairement définis dans leur contexte.

Même dans le troisième cas, imaginez à quel point il est facile de mélanger `expected` et `actual` !
:::

:::{important} Choisir des noms convenables
:class: dropdown
Il est important de choisir des verbes et des mots-clés qui reflètent clairement l'intention de la fonction. Par exemple, une fonction qui transforme une entrée et retourne une sortie ne devrait pas être nommée de la même manière qu'une fonction void déclenchant un événement.

L'ordre des arguments dans une fonction à deux paramètres peut également être précisé dans le nom de la fonction. Par exemple :
```{code} cpp
bool assertExpectedEqualsActual(const SomeType& expected, const SomeType& actual);
``` 
:::

```{code} cpp
Circle makeCircle(double xCoordinate, double yCoordinate, double radius);
```

:::{danger} Trois arguments 
Dans le même esprit que précédemment, l'utilisation de fonctions à trois arguments doit être soigneusement justifiée. Si vous pouvez refactoriser, il est souvent préférable de refactoriser !
```{code} cpp
struct Point{
    double xCoordinate;
    double yCoordinate;
}

Circle makeCircle(Point center, double radius);
```
:::

:::{error} Plus que trois arguments 
:class: dropdown
Si vous avez plus de trois arguments, il est souvent préférable de regrouper certains d'entre eux sous une même classe, comme illustré dans l'exemple précédent.
:::

### Pas d'effets secondaires 

```{code} cpp
void activateUser(const vector<User>& users, int validUserId) {
    users[validUserId].activate();
    sendActivationNotification(users[userId].getName() + "@example.com");
}
```

:::{error} Problème 
:class: dropdown
La fonction précédente promet 'l'activation d'un utilisateur', mais elle envoie également un email de notification, ce qui constitue un effet secondaire. 

Il est crucial d'éviter les effets secondaires pour garantir la responsabilité unique.
:::

```{code} cpp
void activateUserAndSendNotification(const vector<User>& users, int validUserId) {
    users[validUserId].activate();
    sendActivationNotification(users[userId].getName() + "@example.com");
}
```

:::{error} Problème
:class: dropdown
Ceci est une fausse solution, car cette fonction fait deux choses. 

La véritable solution consiste à déplacer l'envoi de la notification dans une autre fonction, probablement celle qui déclenche la procédure d'activation. Cette fonction devrait appeler `activateUser` puis `sendActivationNotification` séparément.
:::