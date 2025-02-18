# TP3 : Initiation à la refactorisation

:::{important} Dernier rappel Git de ce TP
:class: dropdown
Maintenez vos dépôts !  
:::

:::{important} Résolution de conflits
:class: dropdown
Vous avez suivi une initiation à la résolution de conflits en Projet SAÉ. Essayez de résoudre les conflits par vous-même. En cas de difficulté, sollicitez votre encadrant.
:::

:::{important} Ignorer la gestion des erreurs pour le moment
:class: dropdown
Nous n'avons pas encore abordé la gestion des erreurs en classe. Pour le moment, vous pouvez supposer que les arguments sont toujours valides et ne pas vous préoccuper de la gestion des erreurs.
:::

:::{important} Compiler et exécuter régulièrement
:class: dropdown
N'oubliez pas de compiler et exécuter votre code régulièrement lors de la refactorisation. Le comportement du code doit rester cohérent avec vos attentes !
:::

:::{important} Organisation du code
Dans ce TP, nous allons apprendre à réorganiser le code. Il ne s'agit plus de créer un seul fichier `.cpp` contenant tout le code. Suivez les instructions pour organiser les fichiers et le code de manière appropriée.
:::

:::{important} Utilisation d'un IDE 
Les codes deviennent de plus en plus complexes.  
Une bonne coloration syntaxique, une autocomplétion et un *Search and Replace* peuvent vous faire gagner beaucoup de temps.  
Utilisez donc un IDE (comme VSCodium) sans son intégration Git pour le moment.  

Quelques points utiles à connaître sur VSCodium :  
- Ouvrez le répertoire Git (la racine de votre dépôt) via **File** > **Open Folder** et restez sur **Explorer** (première icône de la barre à gauche).  
- Vous pouvez créer des fichiers et des dossiers à l'aide des boutons situés à droite du nom de la racine de votre dépôt.  
- Un clic droit sur un fichier permet d'ouvrir un terminal dans le répertoire du fichier avec **Open in Integrated Terminal**.  
- Utilisez `Ctrl+h` sur une sélection de code pour effectuer un **Search and Replace** dans le fichier courant.  
:::

(tp3-objectifs)=
## Objectifs

Le but de ce TP est de comprendre les points suivants :
- [ ] [Organisation et découpage du code](#tp3-organisation-code)
- [ ] [Principe de responsabilité unique](#cm3-unique-responsibility)
- [ ] [The Stepdown Rule](#cm3-stepdown-rule)
- [ ] [Fonctions à peu d'arguments](#cm3-peu-arguments)
- [ ] [`const` et `&`](#tp3-const-and-ref)

## Exercice 1 : Short functions

### Organisation du code

:::{warning} Théorie à venir !
Cette partie nécessite des manipulations simples, mais aussi une compréhension approfondie de la théorie de la compilation en C++ (applicable à certains langages de la famille C, mais pas à d'autres comme C#, Java, Python, ou Javascript) pour saisir l'intérêt de cette organisation.  

Si vous souhaitez simplement apprendre les bonnes pratiques de manière générale, vous pouvez parcourir plus rapidement la théorie de C++ et vous concentrer sur les points essentiels (résumés à la fin de la sous-section).  
:::

1. Créez un répertoire `short-functions/` dans `TP3/`.

2. Créez les fichiers suivants dans `short-funtions/` :
- `display-prices.cpp`
- `display-prices.h`
- `main.cpp`
- `product.cpp`
- `product.h`

:::{important} Pourquoi ne pas regrouper tout le code dans un seul fichier ?
Lorsque le projet devient plus complexe qu'un simple exercice de code, la séparation du code apporte les mêmes avantages que les principes de propreté et de développement abordés en cours : modularité, réutilisabilité, maintenabilité, extensibilité, encapsulation, lisibilité, gestion des erreurs, ...  
:::

3. Recopiez les codes suivants dans les fichiers correspondant.

`product.h` :
```{code} cpp
#ifndef PRODUCT_H
#define PRODUCT_H

#include <string>

class Product {
private:
    std::string mName;
    double mPrice; // in euros
    int mQuantity; // in units

public:
    Product(const std::string& name, double price, int quantity);
    std::string getName() const;
    double getPrice() const;
    int getQuantity() const;
    bool operator<(const Product& other) const;
};

#endif
```

:::{important} Où est `using namespace std` ?
:class: dropdown
`using namespace std` évite de réécrire `std::` devant tous les types, fonctions ou objets de la bibliothèque standard de C++.  
Cependant, lorsque le projet devient plus complexe, des conflits peuvent survenir si une fonction (ou un type/objet) porte le même nom qu'une fonction de la bibliothèque standard.  
Nous éviterons donc l'utilisation de `using namespace std` à partir de maintenant.  
:::

:::{important} Fichiers header `.h` en C++
:class: dropdown
En C++, les fichiers header (`.h`) contiennent les déclarations de fonctions, de classes et de variables, mais pas leur définition.  
Cette séparation entre déclaration et définition permet à C++ de gérer les dépendances et de modulariser le code.  
Par exemple, si un fichier source `.cpp` a besoin de l'objet `Product`, il suffit d'inclure (`#include`) `"product.h"` sans se soucier de l'implémentation, car `product.h` "promet" que les attributs, le constructeur et les méthodes déclarés seront implémentés.  
:::

:::{important} Les directives du préprocesseur en C++
:class: dropdown
Cette partie est appelé les directives :
```{code} cpp
#ifndef PRODUCT_H
#define PRODUCT_H

#include <string>

//...

#endif
```
:::

:::{note} Compilation en C++
:class: dropdown
Pour bien organiser un code C++ et en comprendre l'intérêt, il est essentiel de saisir le fonctionnement de la compilation en C++.  
En réalité, le terme "compilation" regroupe 4 étapes pour chaque fichier source `.cpp` :  
1. **Preprocessing** : les **directives** sont exécutées. Par exemple, `#include` est remplacé par le contenu du fichier inclus.  
2. **Translation Unit** : le fichier prétraité devient un grand fichier texte appelé *translation unit*.  
3. **Compilation** : le *translation unit* est compilé en code machine dans un fichier objet (`.o`).  
4. **Linking** : les fichiers objets sont liés en fonction de leurs dépendances (headers et fichiers sources correspondants) pour former l'exécutable.  
:::

:::{important} Pourquoi séparer les headers et le code source en C++ ?
:class: dropdown
- Imaginons un code source contenant à la fois les déclarations et l'implémentation (ce dont nous avons l'habitude) d'une classe que nous devons réutiliser dans plusieurs fichiers pour construire notre exécutable.  
- La bonne pratique consiste à compiler tous les fichiers `.cpp`, plutôt que de tenter de suivre les dépendances entre les fichiers et risquer de se perdre pour éviter de tout compiler.  
- Ce code sera donc compilé au moins deux fois : une première fois en tant que fichier `.cpp` et une autre lors de l'inclusion dans un autre fichier `.cpp`.  
- C++ suit la **règle de la définition unique**, qui stipule que pour un exécutable donné, si les portées de deux variables ou fonctions se croisent, elles doivent être différentes.  
- Lors du linking, C++ détecte une erreur parce que l'exécutable contient des définitions en double provenant de différentes translation units.  
- Un header n'est pas compilé comme un fichier source car il contient uniquement des déclarations, pas les définitions elles-mêmes. Lorsque le header est inclus dans les fichiers sources `.cpp` via `#include`, des déclarations identiques peuvent apparaître plusieurs fois, mais **C++ autorise plusieurs déclarations d'un même élément avec des portées différentes ou venant de translation units différentes** (tant qu'il n'y a qu'une seule définition).  
- Il ne faut donc **jamais inclure un `.cpp`, mais seulement des `.h`**.
:::

:::{important} Include guards
:class: dropdown
Ces directives ensemble forment un **include guard** :
```{code} cpp
#ifndef PRODUCT_H
#define PRODUCT_H

//...

#endif
```
- `#define` crée une **macro** qui remplace le texte défini (`PRODUCT_H`) par le code qui suit. La convention de nommage des macros est la même que celle des constantes globales et inclut `_H` pour indiquer qu'il s'agit d'une macro liée à l'include guard d'un header (bien qu'il puisse exister d'autres types de macros).  
- `#ifndef` (abréviation de "if not defined"), `#define` et `#endif` signifient : "Si la macro PRODUCT_H n'est pas définie, définissez la macro PRODUCT_H comme suit : `<code>`, fin de la condition".  

L'include guard permet à un header d'être inclus une seule fois dans chaque code source et donc dans chaque translation unit.  

L'inconvénient des include guards est la nécessité d'avoir un schéma de nommage cohérent pour un exécutable (dont la compilation peut provenir de milliers de fichiers différents répartis dans différents répertoires) : il ne peut pas y avoir deux headers avec la même macro (même si les fichiers se trouvent dans des répertoires très éloignés les uns des autres).  

Une autre directive est parfois utilisée à la place des include guards, mais elle présente aussi ses propres inconvénients.  
Les bonnes pratiques standard en C++ recommandent l'utilisation des include guards.  
:::

`product.cpp` :
```{code} cpp
#include "product.h"
#include <string>

Product::Product(const std::string& name, double price, int quantity) 
    : mName(name), mPrice(price), mQuantity(quantity) {}
    
std::string Product::getName() const { 
    return mName; 
}
        
double Product::getPrice() const { 
    return mPrice; 
}

int Product::getQuantity() const { 
    return mQuantity; 
}

bool Product::operator<(const Product& other) const {
    return mPrice < other.mPrice;
}   
```

:::{important} Pourquoi `#include <string>` une "deuxième" fois ?
:class: dropdown
Si `product.h` inclut `<string>` et que `product.cpp` inclut `product.h`, pourquoi devons-nous inclure `<string>` une deuxième fois ?  
**Include what you see** est une bonne pratique en C++.  

Tout d'abord, les headers ne contiennent pas toujours toutes les bibliothèques nécessaires pour les fichiers sources, car certaines bibliothèques peuvent ne pas être nécessaires pour une déclaration, mais seront indispensables pour la définition.  
De plus, vérifier que toutes les bibliothèques nécessaires sont déjà incluses dans les headers est une perte de temps lorsqu'il y a un grand nombre de headers. En revanche, un include en trop ne pose pas de problème si les include guards sont utilisés (ce qui est le cas pour toutes les bibliothèques C++).
:::

:::{important} `Product::`
:class: dropdown
Il est nécessaire d'écrire `Product::` devant toutes les méthodes de `Product` pour indiquer à C++ que ces éléments sont bien internes à `Product`. Il est possible de définir des fonctions externes à `Product` dans ce même fichier, mais ce n'est pas une bonne pratique.
:::

:::{important} `operator<`
La méthode `operator<` est un **override** de la comparaison par défaut `<`.  
Cela signifie que les objets `Product` ne peuvent pas être comparés avec `<` de manière traditionnelle. Nous redéfinissons cette comparaison en disant qu'un objet `Product` est plus petit qu'un autre (`other`) si son prix est plus petit (`mPrice < other.mPrice`).  
Cela permettra de trier les produits en fonction de leur prix plus tard.
:::

`display-prices.h` :
```{code} cpp
#ifndef DISPLAY_PRICE_H
#define DISPLAY_PRICE_H

#include "product.h"

void displayAvailableProductsByNonDecreasingPriceAndDisplayTotalPrice();

#endif
```

`display-prices.cpp` :
```{code} cpp
#include "product.h"
#include "display-prices.h"
#include <iostream>
#include <vector>
#include <algorithm>

void displayAvailableProductsByNonDecreasingPriceAndDisplayTotalPrice(){
	
    // Fetching products data
    std::vector<Product> products;
    // Our current products
    products.push_back(Product("Laptop", 1000, 5));
    products.push_back(Product("Smartphone", 700, 0));
    products.push_back(Product("Tablet", 500, 2));
    products.push_back(Product("Headphones", 150, 10));
    products.push_back(Product("Smartwatch", 200, 0));

    // Fetching available products data
	std::vector<Product> availableProducts;
    for (const Product& product : products) {
        if (product.getQuantity() > 0) {
            availableProducts.push_back(product);
        }
    }

    // Sorting available products by non-decreasing price
	sort(availableProducts.begin(), availableProducts.end());

    // Calculating total price
    double totalPrice = 0;
    for (const Product& product : availableProducts) {
        totalPrice += product.getPrice() * product.getQuantity();
    }

    // Displaying available products by non-decreasing price
	std::cout << "Sorted Available Products (by non-decreasing price):" << std::endl;
    for (const Product& product : availableProducts) {
        std::cout << "Name: " << product.getName() << " - Price: " << product.getPrice() 
             << " - Quantity: " << product.getQuantity() << std::endl;
    }

    // Displaying total price
    std::cout << "Total Price of Available Products: " << totalPrice << std::endl;
}
```

:::{note} `for (const Product& product : products)`
:class: dropdown
Il est possible de faire une boucle `for` avec la syntaxe `for (ElementType element : vectorOfElements)`.
:::

`main.cpp` :
```{code} cpp
#include "display-prices.h"

int main() {
    displayAvailableProductsByNonDecreasingPriceAndDisplayTotalPrice();
    return 0;
}
```
:::{important} Découpage du code
:class: dropdown
C'est une bonne pratique de découper le code en classes, fonctions externes liées et le main (chacun ayant son propre header, à l'exception de `main`).  
Des découpages plus fins des fonctions externes peuvent être réalisés si une partie de ces fonctions doit être réutilisée ailleurs (modularité et réutilisabilité).
:::

4. Compilez le code avec `g++ -o short-functions main.cpp display-prices.cpp product.cpp` (l'ordre des `.cpp` n'a pas d'importance).

:::{note} Version C++
:class: dropdown
Si vous voyez des warnings lors de la compilation liés aux standards de syntaxe utilisés (par exemple, `for (ElementType element : vectorOfElements)`, qui n'existe que depuis C++11), vous pouvez compiler avec une version plus récente de C++ compatible avec votre compilateur `g++`.

Pour vérifier la version (l'année) de C++ utilisée par défaut par `g++` sur votre poste de travail, utilisez la commande suivante :
```{code} sh
g++ -dM -E -x c++ /dev/null | grep __cplusplus | sed 's/[^0-9]*\([0-9]\{4\}\)[0-9]*L/\1/'
```
Par exemple, `2011` correspond à C++11.

Pour vérifier les versions compatibles avec `g++` sur votre poste, utilisez la commande :
```{code} sh
g++ -v --help 2> /dev/null | grep -oP '(?<=-std=)c\+\+\S+' | sed 's/\.$//' | sort | uniq
```
Les dernières versions de C++ après 11 sont C++14, 17, 20 et 23.

Vous pouvez compiler votre code en utilisant une version plus récente et compatible que celle par défaut. Par exemple :
```{code} sh
g++ -std=c++14 -o short-functions main.cpp display-prices.cpp product.cpp
```
:::

5. Exécutez `./short-functions`.

(tp3-organisation-code)=
:::{important} Résumé des bonnes pratiques d'organisation du code en C++
- Ne pas tout coder dans un seul fichier.  
- Utiliser un fichier `.cpp` et un fichier `.h` de même nom pour chaque classe et chaque groupe de fonctions externes liées, puis un fichier `main.cpp`.  
- Un fichier (`.cpp` et `.h`) d'une classe porte le nom de la classe, et un fichier (`.cpp` et `.h`) de fonctions externes porte le nom qui les relie.  
- Les fichiers `.h` doivent contenir uniquement les déclarations, et les fichiers `.cpp` doivent contenir les définitions.  
- Les fichiers `.h` doivent toujours avoir des **include guards**, c'est-à-dire :  
```cpp
#ifndef NOM_DE_HEADER_H
#define NOM_DE_HEADER_H

//...

#endif
```  
- Ne pas oublier d'utiliser `MyClass::` devant les méthodes dans `my-class.cpp` qui définit la classe `MyClass` déclarée dans `my-class.h`.  
- Si vous avez besoin des éléments définis dans `code.cpp`, alors `#include "code.h"` (qui déclare ces éléments), et non le `code.cpp` lui-même.  
- **Include what you see** : ajouter les bibliothèques et headers que vous utilisez pour votre code au début, peu importe si un header que vous incluez les contient déjà.  
- Ne plus utiliser `using namespace std`.  
:::

:::{warning} Java et C#
- En Java et C#, il n'y a pas de séparation entre les headers et les codes source, car leurs systèmes de compilation diffèrent. Les éléments du code sont à la fois déclarés et définis dans le même fichier.
- Néanmoins, il reste essentiel d'organiser le code en plusieurs fichiers, de la même manière qu'en C++.
- En Java et C#, il est nécessaire de nommer un fichier contenant une classe en fonction du nom de la classe (comme en C++), mais en utilisant la convention **UpperCamelCase** (également appelée **PascalCase**). De plus, en Java, la compilation échouera si le fichier contenant une classe n'est pas correctement nommé, contrairement à C++.
- En Java et C#, on suit plutôt le principe de **Include what you use**, qui consiste à importer (`import` en Java, `using` en C#) uniquement ce qui est nécessaire au bon fonctionnement du code. Cela diffère du principe **Include what you see** en C++, qui repose sur les include guards pour éviter les duplications de code.
:::

### Refactorisation du code

1. Que fait ce code ?

:::{note} `sort`
La fonction `sort` trie un `vector` en place (aucune copie du `vector` n'est créée). Elle prend en argument l'indice de début et de fin du `vector` et utilise la comparaison `<` par défaut (qui a été redéfinie pour `Product`). Elle trie donc les valeurs de manière croissante (*non-decreasing* en anglais, *increasing* désignant un ordre strictement croissant). 
:::

(tp3-const-and-ref)=
:::{hint} `const` et `&`
:class: dropdown
L'utilisation de `const` pour les arguments des fonctions garantit que la fonction ne modifiera pas l'argument `const`. La référence `&` pour les arguments permet de travailler sur le même objet que celui passé à la fonction, évitant ainsi de créer une copie de l'objet. Cela permet aussi d'éviter de copier des objets "lourds". Ici, `Product` a trois attributs de type `string`, `double` et `int`. `double` et `int` sont considérés comme "légers", tandis que `string` peut être "lourd".
:::

2. **Quiz** : Quels sont les problèmes du code de `displayAvailableProductsByNonDecreasingPriceAndDisplayTotalPrice` ?

3. Refactorisez le code (en modifiant les autres fichiers concernés comme le header, le main).

:::{hint} Avez-vous accompli votre tâche ?
:class: dropdown
- Avez-vous respecté **The Stepdown Rule** ?  
- Vos fonctions sont-elles bien nommées ?  
- Vos fonctions ont-elles une responsabilité unique ?  
- Vos fonctions ont-elles peu d'arguments ?  
- Vos fonctions avec un argument sont-elles bien classées dans les catégories vues en cours ?  
- Avez-vous placé `const` et `&` aux bons endroits ?
:::

:::{note} Informations codées en dur
Nous allons ignorer le fait que les informations sur les produits sont codées en dur.  
Nous allons imaginer que ces informations proviennent d'une base de données.
:::

## Exercice 2 : Classify it

1. Créez un répertoire `classify-it/` (dans lequel le code sera organisé dans plusieurs fichiers différents).

2. Dans `classify-it/`, créez un fichier `main.cpp` dans lequel vous recopiez le code suivant :

```{code} cpp
#include <iostream>
#include <string>
#include <vector>

double calculateAverage(const std::vector<double>& scores) {
    double sum = 0;
    for (double score : scores) {
        sum += score;
    }
    return sum / scores.size();
}

void printScores(const std::string& studentName, int studentId, const std::vector<double>& scores) {
    std::cout << std::endl << "Scores for " << studentName << " (ID: " << studentId << "): ";
    for (size_t i = 0; i < scores.size(); i++) {
        std::cout << "[" << i+1 << "] " << scores[i] << " ";
    }
    std::cout << std::endl;
}

void printAverage(const std::vector<double>& scores){
    std::cout << "Average: " << calculateAverage(scores) << std::endl;
}

int main() {
    std::string studentName;
    int studentId;
    std::vector<double> scores;

    studentId = 12345;
    studentName = "Alice";

    scores.push_back(11);
    scores.push_back(12.5);
    scores.push_back(14.75);
    scores.push_back(19);

    printScores(studentName, studentId, scores);

    return 0;
}
```

2. Que fait ce code ?

:::{note} `size_t`
:class: dropdown
`size_t` est le type par défaut de l'indice d'un `vector` au lieu de `int` car un `vector` peut être très grand et ses indices peuvent dépasser la limite de `int`.
:::

Il est difficile d'ajouter des étudiants et leurs notes.  
Nous allons donc créer une classe `Student` avec les trois attributs suivants :  
- Seul l'ID de l'étudiant est nécessaire pour construire l'objet. L'ID est **immutable** : une fois initialisée par le constructeur, elle ne peut plus être modifiée. Cela est rendu possible en utilisant `const` devant l'attribut.

:::{note} Initialiser un attribut `const`
Un attribut immutable (`const`) ne peut être initialisé que dans l'**initializer list** en C++.
Par exemple :
```{code} cpp
class MyClass{
private:
    const int mAttribute;
public:
    MyClass(int valueName) : mAttribute(valueName){
        // Constructor body
    }
}
```
Cet attribut ne peut pas être initialisé dans le *Constructor body*.  
Par exemple, le code suivant retournera une erreur de compilation :
```{code} cpp
class MyClass{
private:
    const int mAttribute;
public:
    MyClass(int valueName) {
        mAttribute = valueName;
    }
}
```
:::

- Le nom de l'étudiant peut être modifié. Au début, il est vide (par défaut, un `string someString` non initialisé est `== ""`). Nous n'allons pas gérer les erreurs potentielles venant d'un nom vide dans cet exercice.

:::{hint} Setter
:class: dropdown
Un setter typique ressemble à ceci :
```{code} cpp
void setAttribute(const AttributeType& valueName){
    mAttribute = valueName;
}
```
:::

- Le `vector` des notes de l'étudiant ne sera pas accessible. Les seuls accès se feront à travers l'affichage (`printScores`) et l'ajout de notes via la fonction `appendScore`, qui ajoute une note à la fin du `vector` en utilisant `push_back`. Il n'y a pas de notes au début, mais nous allons aussi ignorer les erreurs potentielles venant d'un `vector` vide dans cet exercice.

3. Créez la classe `Student` avec les méthodes appropriées (en incluant les fichiers `.h` et `.cpp` correspondants), et ajoutez-y les fonctions pour calculer la moyenne et afficher les notes.

4. Modifiez les directives de `main.cpp` pour inclure `student.h` et les bibliothèques nécessaires.

:::{hint} Avez-vous accompli votre tâche ?
:class: dropdown
- Les fichiers `.h` et `.cpp` respectent-ils les bonnes pratiques ?
- Les attributs sont-ils bien nommés (sans préfixes inutiles) ?
- Les getters et setters correspondent-ils à notre cas d'utilisation ?
- Avez-vous transformé les fonctions en méthodes de la classe de manière appropriée ?
- Avez-vous vérifié si les méthodes qui ne modifient pas l'objet sont marquées `const` ?
:::

### User input (Bonus)

L'intérêt d'avoir une classe `Student` est de faciliter l'entrée des données de plusieurs étudiants.  
Pour pouvoir saisir les informations de plusieurs étudiants, nous allons écrire plusieurs fonctions pour gérer les saisies utilisateur.

1. Créez un fichier `user-input.cpp` ainsi que le fichier header associé.

2. Voici des déclarations de fonctions pour vous aider :
```{code} cpp
void inputNumberOfStudents(int& number);
void inputStudentId(int& id);
void inputStudentName(Student& student);
void inputStudentScores(Student& student);
Student createStudentFromInput();
void printAllScoresAndAverages(const std::vector<Student>& students);
```

3. Voici le code de `inputStudentScores` pour vous aider :
```{code} cpp
void inputStudentScores(Student& student) {
    double score;
    std::cout << "Enter score (-1 to stop): ";
    std::cin >> score;

    while (score != -1) {
        student.appendScore(score);
        std::cout << "Enter score (-1 to stop): ";
        std::cin >> score;
    }
}
```

:::{note} `cin >>`
`cin >>` affecte uniquement les valeurs saisies au clavier jusqu'à rencontrer un espace.  
Par exemple, si vous tapez `Jean Pierre`, `cin >>` retournera uniquement `Jean`.  
Pour éviter les détails de lecture (*parsing*) de l'entrée, nous supposerons qu'un nom est un seul mot ou plusieurs mots écrits en UpperCamelCase.
:::

4. Implémentez les fonctions données et modifiez le `main` pour récupérer les saisies utilisateur et afficher les notes ainsi que les moyennes de chaque étudiant.

Revenez aux [objectifs](#tp3-objectifs) et cochez les points que vous avez maîtrisés. Revenez sur les points que vous n'avez pas encore bien compris. Appelez votre encadrant si besoin.