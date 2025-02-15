# TP3 : Initiation à la refactorisation

:::{important} Dernier rappel Git de ce TP
:class: dropdown
Maintenez vos dépôts !  
:::

:::{important} Résolution de conflits
:class: dropdown
Vous avez suivi une initiation à la résolution de conflits en Projet SAÉ.  
Essayez de résoudre les conflits par vous-même.  
En cas de difficulté, sollicitez votre encadrant.
:::

:::{important} Ignorer la gestion des erreurs pour le moment
:class: dropdown
Nous n'avons pas encore abordé la gestion des erreurs en classe.  
Pour le moment, vous pouvez supposer que les arguments sont toujours valides et ne pas vous préoccuper de la gestion des erreurs.
:::

:::{important} Compiler et exécuter régulièrement
:class: dropdown
N'oubliez pas de compiler et exécuter votre code régulièrement lors de la refactorisation.  
Le comportement du code doit rester cohérent avec vos attentes !
:::

:::{important} Organisation du code
Dans ce TP, nous allons apprendre à réorganiser du code. 
Il ne faut donc plus créer un seul fichier `.cpp` contenant tout le code.
Suivez les instructions pour l'organisation des fichiers et du code.
:::

:::{important} Utilisation d'un IDE 
Les codes deviennent de plus en plus complexes.
Une bonne coloration syntaxique, une auto-complétion, un simple *Search and Replace* peut vous sauvez beaucoup de temps.
Utilisez donc un IDE (comme VSCodium) sans son intégration Git pour le moment.

Quelques points utiles à savoir sur VSCodium:
- Ouvrez le répertoire Git (la racine de votre dépôt) avec **File** > **Open Folder** et restez sur **Explorer** (premier icône de la barre à gauche).
- Vous pouvez créer des fichiers et des dossiers avec des boutons à droite du nom de la racine de votre dépôt.
- Un clic droit sur n'importe quel fichier permet d'ouvrir un terminal dans le répertoire du fichier avec **Open in Integrated Terminal**.
- `Ctrl+h` sur n'importe quelle sélection d'un bout de code permet de faire un **Search and Replace** dans le fichier courant.
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

(tp3-organisation-code)=
### Organisation du code

:::{warning} Théorie à venir !
Cette partie demande des manipulations simples mais beaucoup de théorie de compilation en C++ (qui est applicable seulement à quelques langages de la famille C mais pas à d'autres languages comme C#, Java, Python, Javascript...) pour comprendre l'intérêt de cette organisation.

Si vous voulez juste apprendre les bonnes pratiques de façon générale, vous pouvez passer plus rapidement sur la théorie de C++ et seulement retenir les points importants (qui sont résumé à la fin de la sous-section).
:::

1. Créez un répertoire `short-functions/` dans `TP3/`.

2. Créez les fichiers suivants dans `short-funtions/` :
- `display-prices.cpp`
- `display-prices.h`
- `main.cpp`
- `product.cpp`
- `product.h`

:::{important} Pourquoi ne pas avoir tout le code dans un seul fichier ?
Quand un projet devient plus complexe qu'un simple exercice de code, la séparation du code a les mêmes valeurs que les principes de propreté et de développement que nous avons vu en cours : modularité, réutilisabilité, maintenabilité, extensibilité, encapsulation, lisibilité, gestion des erreurs,... 
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
`using namespace std` permet de ne pas réécrire `std::` devant tous les types/fonctions/objets de la bibliothèque standard de C++.
Quand le projet devient plus complexe, nous pouvons avoir un problème si une fonction (ou un type/objet) porte le même nom qu'une fonction de la bibliothèque standard.
Nous allons donc éviter l'utilisation de `using namespace std` à partir de maintenant.
:::

:::{important} Fichiers header `.h` en C++
:class: dropdown
En C++, les fichiers header (`.h`) contiennent les déclarations de fonctions, de classes et de variables, mais pas leur implémentation.
Cette séparation entre déclaration et implémentation permet à C++ de gérer les dépendences et la modularisation du code.
Par exemple, si un code source `.cpp` a besoin de l'objet `Product`, il suffit d'inclure (#include) `"product.h"` sans se soucier de l'implémentation car `product.h` "promets" que les attributs, constructeur et méthodes déclarées seront implémentés. 
:::

:::{note} Les directives du préprocesseur en C++
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
Pour comprendre comment organiser un code C++ et son intérêt, il faut comprendre comment la compilation fonctionne en C++.
La "compilation" est un abus de langage en C++ qui contient en réalité 4 étapes pour chaque fichier de code source `.cpp` :
1. **Preprocessing** : les **directives** sont exécutées. Par exemple, `#include` est remplacé par le contenu du fichier inclus.
2. **Translation Unit** : le fichier prétraité devient un unique large fichier de texte appelé *translation unit*.
3. **Compilation** : le *translation unit* est compilé en code machine dans un fichier objet (`.o`).
4. **Linking** : les fichiers objets sont liés à travers leur dépendences (header avec code source correspondant) pour former l'exécutable.
:::

:::{important} Pourquoi séparer en header et code source en C++ ?
:class: dropdown
- Imaginons un code source contenant à la fois les déclarations et l'implémentation (ce dont nous avons l'habitude) d'une classe que nous avons besoin de réutiliser dans plusieurs codes différents pour construire notre exécutable.
- La bonne pratique nous dit de compiler tous les fichiers `.cpp` au lieu d'essayer de suivre les dépendances entre les fichiers et se perdre juste pour ne pas tout compiler.
- Ce code sera donc compilé au moins deux fois, une fois parce que c'est un fichier `.cpp` et au moins une autre fois lors d'un include dans un autre fichier `.cpp`.
- C++ suit la **règle de la définition unique** qui dit que pour un exécutable donné, si les portées de deux variables/fonctions intersectent alors elles doivent être différentes.
- Lors du linking, C++ détecte une erreur parce que l'exécutable contient des définitions en double venant de translation units différentes.
- Un header n'est pas compilé comme un code source car il contient seulement des déclarations mais pas les définitions elles-mêmes. Quand un header est recopié dans les codes sources `.cpp` à travers `#include`, des déclarations des mêmes variables/fonctions peuvent apparaître plusieurs fois mais **C++ autorise plusieurs déclarations** tant qu'il a une seule définition.
- Il ne faut donc **jamais include un `.cpp` mais que des `.h`**.
:::

:::{note} Include guards
:class: dropdown
Ces directives ensemble forment ce que nous appelons un **include guard** :
```{code} cpp
#ifndef PRODUCT_H
#define PRODUCT_H

//...

#endif
```
- `#define` crée une **macro** qui remplace le texte défini (`PRODUCT_H`) par le code qui suit. La convention de nommage des macros est la même que celle des constantes globales et elle inclus `_H` pour dire que c'est une macro liée à l'include guard d'une header (il peut exister d'autre type de macro).
- `#ifndef` (une abbréviation de "if not defined"), `#define` et `#endif`  veut dire : "If the macro PRODUCT_H is not defined, then define the macro PRODUCT_H as follows : `<code>`, end if". 

L'include guard permet à un header d'être défini une seule fois dans chaque code source et donc chaque translation unit. Ce n'est pas forcément nécessaire vu que C++ autorise plusieurs déclarations de la même chose à l'intérieur même d'une translation unit mais c'est une bonne pratique d'avoir une seule déclaration par translation unit pour casser des boucles dans les dépendances et rendre la compilation plus efficace car il n'y aura pas de duplications de code.

L'inconvénience des include guards est la nécessité d'avoir un schéma de nommage cohérent pour un exécutable (dont la compilation peut venir de milliers de fichiers différents dans des répertoires différents) : il ne peut pas y avoir deux header avec la même macro (même si les deux fichiers sont dans des répertoires très éloignées les uns des autres).

Une autre directive est parfois utilisée à la place des include guards mais elle présente aussi ses propres inconvéniences.
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
Si `product.h` inclus `<string>` et que `product.cpp` inclus `product.h`, alors pourquoi avons nous besoin d'inclure `<string>` une deuxième fois ?
**Include what you see** est une bonne pratique en C++.

D'abord, les headers ne contiennent pas toujours toutes les bibliothèques nécessaires pour les codes sources parce que nous n'avons pas forcément besoin de certaines bibliothèques pour une déclaration alors que nous en avons besoin pour la définition.
De plus, vérifiez que toutes les bibliothèques nécessaires sont déjà dans les headers est une perte de temps quand le nombre de header est grand alors qu'un include en trop ne fait rien de mal si les include guards sont utilisés (ce qui est le cas de toutes les bibliothèques C++).
:::

:::{important} `Product::`
:class: dropdown
Il est nécessaire d'écrire `Product::` devant toutes les méthodes de `Product` pour indiquer à C++ que ces éléments sont bien internes à `Product`. Il est possible de définir des fonctions externes à `Product` dans ce même fichier mais ce n'est pas une bonne pratique.
:::

:::{important} `operator<`
La méthode `operator<` est un **override** de la comparaison par défaut `<`.
Il s'agit de dire que nos objets (`Product`) ne peuvent pas être comparé avec `<` et nous redéfinissons la comparaison en disant qu'un objet `Product` est plus petit qu'un autre (`other`) si son prix est plus petit (`mPrice < other.mPrice`).
Ceci va servir pour trier les produits selon leur prix plus tard.
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
C'est une bonne pratique de découper le code en classes, fonctions externes liées et le main (chacun avec son propre header à part main).
Des découpages plus fines des fonctions externes peuvent être effectués s'il y a besoin de réutiliser une partie de ses fonctions ailleurs (modularité et réutilisabilité).
:::

4. Compilez le code avec `g++ -o short-functions main.cpp display-price.cpp product.cpp` (l'ordre des `.cpp` n'a pas d'importance).

5. Exécutez `./short-functions`.

:::{important} Résumé des bonnes pratiques d'organisation du code
- Ne pas tout coder dans un seul fichier.
- Utiliser un fichier `.cpp` et un fichier `.h` de même nom pour chaque classe et chaque groupe de fonctions externes liées; puis `main.cpp`.
- Un fichier (`.cpp` et `.h`) d'une classe porte le nom de la classe et un fichier (`.cpp` et `.h`) de fonctions externes porte le nom qui les relie.
- Les fichiers `.h` doivent contenir seulement les déclarations et les fichiers `.cpp` les définitions.
- Les fichiers `.h` doivent toujours avoir des **include guards**, c'est-à-dire :
```{code} cpp
#ifndef NOM_DE_HEADER_H
#define NOM_DE_HEADER_H

//...

#endif
```
- Ne pas oublier d'utiliser `MyClass::` devant les méthodes dans `my-class.cpp` qui définit la classe `MyClass` déclarée dans `my-class.h`.
- Si vous avez besoin du code d'un fichier, include son header (`.h`), pas le code `.cpp` lui-même.
- Include what you see : ajouter les bibliothèques et header que vous utilisez pour votre code au début peu importe si un header que vous incluez les contient déjà.
- Ne plus utiliser `using namespace std`.
:::

### Refactorisation du code

1. Que fait ce code ?

:::{note} `sort`
La fonction `sort` trie un `vector` en place (aucune copie du `vector` n'est créée).  
Elle prend en argument l'indice de début et de fin du `vector` et utilise la comparaison `<` par défaut (qui a été redéfini pour `Product`).  
Elle trie donc les valeurs de manière croissante (*non-decreasing* en anglais, *increasing* désignant un ordre strictement croissant).  
:::

(tp3-const-and-ref)=
:::{hint} `const` et `&`
:class: dropdown
L'utilisation de `const` pour les arguments des fonctions garantit que la fonction ne modifiera pas l'argument `const`.  
La référence `&` pour les arguments permet de travailler sur le même objet que celui passé à la fonction, évitant ainsi de créer une copie de l'objet.  
Cela permet aussi d'éviter de copier des objets "lourds".  
Ici, `Product` a trois attributs de type `string`, `double` et `int`.  
`double` et `int` sont considérés comme "légers", tandis que `string` peut être "lourd".
:::

2. **Quiz** : Quels sont les problèmes de ce code ?

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
Nous allons imaginer que ces informations viennent d'une base de donnée.
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

void printScores(const std::string& studentName, int studentID, const std::vector<double>& scores) {
    std::cout << std::endl << "Scores for " << studentName << " (ID: " << studentID << "): ";
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
    int studentID;
    std::vector<double> scores;

    studentID = 12345;
    studentName = "Alice";

    scores.push_back(11);
    scores.push_back(12.5);
    scores.push_back(14.75);
    scores.push_back(19);

    printScores(studentName, studentID, scores);

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
- Seul l'ID de l'étudiant est nécessaire pour construire l'objet. L'ID est *immutable* : une fois initialisée par le constructeur, elle ne peut plus être modifiée. Cela est rendu possible en utilisant `const` devant l'attribut.  

:::{note} Initialiser un attribut `const`
Un attribut immutable (`const`) ne peut être initialisé que dans l'*initializer list* en C++.
Par exemple :
```{code} cpp
class MyClass{
private:
    const int mAttribute;
public:
    MyClass(valueName) : mAttribute(valueName){
        // Constructor body
    }
}
```
Cet attribut ne peut pas êtré initialisé dans *Constructor body*.
Par exemple, le code suivant retournera une erreur de compilation :
```{code} cpp
class MyClass{
private:
    const int mAttribute;
public:
    MyClass(valueName) {
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

- Le `vector` des notes de l'étudiant ne sera pas accessible. Les seuls accès se feront à travers l'affichage (`printScores`) et l'ajout de notes via la fonction `appendScore`, qui ajoute une note à la fin du `vector` en utilisant `push_back`. Il n'y a pas de notes au début mais nous allons aussi ignorer les erreurs potentielles venant d'un `vector` vide dans cet exercice.

3. Créez la classe `Student` avec les bonnes méthodes (avec les fichiers `.h` et `.cpp` correspondant) et y rajouter les fonctions de calcul de moyenne et d'affichage.

4. Modifiez les directives de `main.cpp` pour inclure `student.h` et les bibliothèques nécessaires.

:::{hint} Avez-vous accompli votre tâche ?
:class: dropdown
- Les fichiers `.h` et `.cpp` respectent-ils les bonnes pratiques ?
- Les attributs sont-ils bien nommés (sans préfixes inutiles) ?  
- Les getters et setters correspondent-ils à notre cas d'utilisation ?  
- Avez-vous transformé les fonctions en méthodes de la classe de la bonne manière ?  
- Avez-vous vérifié si les méthodes qui ne modifient pas l'objet sont marquées `const` ?
:::

### User input (Bonus)

L'intérêt d'avoir une classe `Student` est la possibilité de rentrer les données de plusieurs étudiants plus facilement.
Pour pouvoir saisir les informations de plusieurs étudiants, nous allons écrire plusieurs fonctions pour gérer les user inputs.

1. Créez un fichier `user-input.cpp` et la header associée.

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

4. Implémentez les fonctions données et modifiez le main pour récupérer les user-inputs et afficher les notes et moyennes de chaque étudiant.

Revenez aux [objectifs](#tp3-objectifs) et cochez les points que vous avez maîtrisés. Revenez sur les points que vous n'avez pas encore bien compris. Appelez votre encadrant si besoin.