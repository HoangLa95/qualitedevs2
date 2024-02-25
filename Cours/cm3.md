# Comment écrire une fonction ?

```{figure} ../images/small-functions.jpg
:alt: Fonctions courtes
:align: center

Laisse-moi vous parler plus des fonctions courtes (Credits: programming-memes.com)
```

## La première règle du function club

La première règle du *function club* est qu'une fonction doit être **courte**. La deuxième règle du function club est qu'une fonction doit être **plus courte**.

:::{note} Pourquoi une fonction courte ?
:class: dropdown
Une fonction courte est plus facile à lire et à comprendre. Plus elle est courte, plus c'est facile à voir et comprendre la structure de la fonction et à repérer les erreurs.
:::

Pour vous convaincre, voici une fonction longue :

```{code} cpp
#include <cmath>
#include <vector>

using namespace std;

vector<int> generatePrimes(int maxValue) {
    if (maxValue >= 2) {
        int s = maxValue + 1;
        vector<bool> f(s, true);
        int i;
        f[0] = f[1] = false;
        int j;
        for (i = 2; i < sqrt(s) + 1; i++) {
            if (f[i]) {
                for (j = 2 * i; j < s; j += i)
                    f[j] = false;
            }
        }
        int count = 0;
        for (i = 0; i < s; i++) {
            if (f[i])
                count++;
        }
        vector<int> primes(count);
        for (i = 0, j = 0; i < s; i++) {
            if (f[i])
                primes[j++] = i;
        }
        return primes;
    } else {
        return vector<int>();
    }
}
```

Ce que fait cette fonction :
```{figure} ../images/sieve-animation.gif
:alt: Crible d'Érastosthènes
:align: center

Crible d'Ératosthènes (Credits: https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes)
```

## Comment écrire une fonction courte ?

### Petites boucles et peu d'indentations

:::{important} Longueur d'un bloc
:class: dropdown
Les blocs de codes à l'intérieur d'une boucle doivent souvent être des fonctions à part. La fonction avec la boucle ne fait donc qu'un appel à ces fonctions (souvent avec un nom très descriptif, ce qui rajoute à la clarté du code).
:::

:::{important} Nombre d'intentations
:class: dropdown
Une fonction ne doit pas avoir plus de deux niveaux d'indentations. Plus il y a de structures imbriquées, plus le code est difficile à comprendre.
:::

### Faire une seule chose

> LES FONCTIONS DEVRAIENT FAIRE UNE CHOSE. ELLES DEVRAIENT LA FAIRE BIEN. ELLES DEVRAIENT LA FAIRE SEULEMENT.
> - Robert C. Martin

:::{danger} Les sections dans une fonction
:class: dropdown
Si une fonction a plusieurs sections, alors elle fait trop de choses.
:::

### Un seul niveau d'abstraction

:::{warning} Ne pas mélanger les idées
:class: dropdown
Une fonction avec plusieurs niveau d'abstractions est difficile à comprendre. 

Dans l'exemple précédent, on mélange des idées comme 
- *un multiple doit toujours avoir un facteur premier plus petit que la racine carrée du nombre d'entiers au total* et 
- *je rentre les résultats dans un vecteur à retourner*.
:::

:::{important} *The Stepdown Rule*
:class: dropdown
Une fonction doit se lire comme un sommaire bien organisé. 
- Au même niveau d'abstraction, j'ai les différents chapitres. 
- À l'intérieur d'un chapitre, j'ai les différentes sections. 
- À l'intérieur d'une section, j'ai du contenu qui me parle d'une et d'une seule chose.

Chaque niveau d'abstraction ici correspondrait à une fonction.
:::

Maintenant, appliquons nos leçons sur le code précédent.

```{code} cpp
#include <cmath>
#include <vector>

using namespace std;

class PrimeGenerator {
public:
    vector<int> generatePrimes(int maxValue) {
        if (maxValue < 2)
            return vector<int>();
        else {
            uncrossIntegersUpTo(maxValue);
            crossOutMultiples();
            putUncrossedIntegersIntoResult();
            return result;
        }
    }
    
private:
    vector<bool> crossedOut;
    vector<int> result;

    void uncrossIntegersUpTo(int maxValue) {
        crossedOut = vector<bool>(maxValue + 1, false);
    }

    void crossOutMultiples() {
        int limit = determineIterationLimit();
        for (int i = 2; i <= limit; i++)
            if (!crossedOut[i])
                crossOutMultiplesOf(i);
    }

    int determineIterationLimit() {
        double iterationLimit = sqrt(crossedOut.size());
        return floor(iterationLimit);
    }

    void crossOutMultiplesOf(int i) {
        for (int multiple = 2 * i; multiple < crossedOut.size(); multiple += i)
            crossedOut[multiple] = true;
    }

    void putUncrossedIntegersIntoResult() {
        result.clear();
        for (int i = 2; i < crossedOut.size(); i++)
            if (!crossedOut[i])
                result.push_back(i);
    }
};
```

## Comment écrire une fonction simple ?

### Peu d'arguments

:::{hint} Le nombre idéal d'arguments d'une fonction :
:class: dropdown
0 !
:::

```{code} cpp
bool fileExists(string& filename)
ifstream fileOpen(string& filename)
void passwordAttemptFailedNtimes(int attempts)
```

:::{important} Un seul argument
:class: dropdown
Il y a trois types de fonctions avec un seul argument.
1. Une fonction qui pose une question.
2. Une fonction qui transforme l'entrée et qui le retourne.
3. Une fonction qui déclenche un évènement sans rien retourner.

Éviter les fonctions à un argument qui ne sont pas dans une des catégorie précédente.
:::

```{code} cpp
void switchPower(bool onOrOff)
```

:::{danger} *Flag argument*
:class: dropdown
Si une fonction prend un booléen en argument, alors la fonction fait au moins **DEUX** choses : une quand le booléen est vrai, l'autre quand il est faux.

Dans ce cas, c'est mieux de séparer la fonction en deux. Par exemple,
```{code} cpp
void turnPowerOn()
void turnPowerOff()
```
Si vraiment la séparation crée des duplications de codes, alors l'utilisation d'un flag peut être justifiée. Mais il faut y faire attention !
:::

```{code} cpp
Point createPoint(int xCoordinate, int yCoordinate)
Matrix multiply(Matrix leftMatrix, Matrix rightMatrix)
bool verifyBehavior(SomeType expected, SomeType actual)
```

:::{warning} Deux arguments
:class: dropdown
L'utilisation de fonctions à deux arguments peut-être dangereux sauf quand les deux arguments ont un ordre naturel ou quand ils ont du sens dans leur contexte. 

Même dans le troisième cas, imaginer à tel point il est facile de mélanger `expected` et `actual` !
:::

```{code} cpp
Circle makeCircle(double xCoordinate, double yCoordinate, double radius)
```

:::{danger} Trois arguments 
:class: dropdown
Sur le même principe que précédemment, il faut que l'utilisation des fonctions à trois arguments soit bien justifiée. Si vous pouvez faire moins, il faut toujours faire moins !
```{code} cpp
Circle makeCircle(Point center, double radius)
```
:::

:::{error} Plus que trois arguments 
:class: dropdown
Si vous avez plus que trois arguments, alors souvent plusieurs arguments peuvent être regroupés sous une même classe comme dans l'exemple précédent.
:::

:::{important} Choisir des noms convenables
:class: dropdown
Il faut choisir des verbes et des mots-clés qui explicitent l'intention de la fonction. Par exemple, une fonction qui transforme un input et retourne un output ne devrait pas être nommé comme une fonction `void` qui déclenche un évènement.

L'ordre des variables pour des fonctions à plusieurs arguments peut aussi être explicité dans le nom de la fonction. Par exemple,
```{code} cpp
bool assertExpectedEqualsActual(SomeType expected, SomeType actual)
```
:::

### Pas d'effets secondaires 

```{code} cpp
bool checkPassword(const string& userName, const string& password) {
    User user = findByName(userName);
    if (user != NULL_USER) {
        string codedPhrase = user.getPhraseEncodedByPassword();
        Cryptographer cryptographer;
        string phrase = cryptographer.decrypt(codedPhrase, password);
        if ("Valid Password" == phrase) {
            Session::initialize();
            return true;
        }
    }
    return false;
}
```

:::{error} Problème 
:class: dropdown
La fonction précédente promets "une vérification de mot de passe" qui retourne donc `true` ou `false` mais elle initialise aussi une session, ce qui voudrait dire qu'elle fait aussi autre chose que ce qu'elle promet. Il s'agit d'un effet secondaire et il faut absolument l'éviter.

Dans ce cas, une fausse solution est de renommer la fonction `checkPasswordandInitializeSession` car elle ne ferait donc pas une seule chose.

La bonne solution est de faire l'initialisation de la session ailleurs, probablement dans une fonction qui appelle `checkPassword` et qui initialise la session seulement si `checkPassword` retourne vrai.
:::

### Pas de répétitions

:::{error} Le mal absolu
:class: dropdown
Presque toutes les principes de programmation que vous apprenez servent à éviter de la duplication de code. Bien nommer les choses, structurer le code, regrouper des éléments à l'intérieur d'une classe... Tout sert à éliminer la duplication qui est une grosse source d'incohérence et donc d'erreurs en programmation.
:::