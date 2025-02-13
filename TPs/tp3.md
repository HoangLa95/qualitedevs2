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

:::{important} Gestion des erreurs
:class: dropdown
Nous n'avons pas encore abordé la gestion des erreurs en classe.  
Pour le moment, vous pouvez supposer que les arguments sont toujours valides et ne pas vous préoccuper de la gestion des erreurs.
:::

:::{important} Tests
:class: dropdown
N'oubliez pas de compiler et de tester votre code régulièrement lors de la refactorisation.  
Les résultats doivent rester cohérents !
:::

(tp3-objectifs)=
## Objectifs

Le but de ce TP est de comprendre les points suivants :
- [ ] [Principe de responsabilité unique](#cm3-unique-responsibility)
- [ ] [The Stepdown Rule](#cm3-stepdown-rule)
- [ ] [Fonctions à peu d'arguments](#cm3-peu-arguments)
- [ ] [`const` et `&`](#tp3-const-and-ref)

## Exercice 1 : Classify it

```{code} cpp
#include <iostream>
#include <vector>

using namespace std;

double calculateAverage(const vector<double>& scores) {
    double sum = 0;
    for (double score : scores) {
        sum += score;
    }
    return sum / scores.size();
}

void printScores(const string& studentName, const int studentID, const vector<double>& scores) {
    cout << endl << "Scores for " << studentName << " (ID: " << studentID << "): ";
    for (size_t i = 0; i < scores.size(); i++) {
        cout << "[" << i+1 << "] " << scores[i] << " ";
    }
}

void printAverage(const vector<double>& scores){
    cout << endl << "Average: " << calculateAverage(scores) << endl;
}

int main() {
    string studentName;
    int studentID;
    vector<double> scores;

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

1. Que fait ce code ?

Il est difficile d'ajouter des étudiants et leurs notes.  
Nous allons donc créer une classe `Student` avec les trois attributs suivants :  
- Seul l'ID de l'étudiant est nécessaire pour construire l'objet. L'ID est *immutable* : une fois initialisée par le constructeur, elle ne peut plus être modifiée. Cela est rendu possible en utilisant `const` devant l'attribut.  
- Le nom de l'étudiant peut être modifié (et l'objet peut être créé sans nom).  
- Le `vector` des notes de l'étudiant ne sera accessible que pour l'affichage (`printScores`) et l'ajout de notes via la fonction `appendScore`, qui ajoute une note à la fin du `vector` en utilisant `push_back`.  

:::{hint} Setter
:class: dropdown
Un setter typique ressemble à ceci :
```{code} cpp
void setAttribute(const AttributeType& valueName){
    mAttribute = valueName;
}
```
:::

2. Créez la classe `Student`.

:::{hint} Avez-vous accompli votre tâche ?
:class: dropdown
- Les attributs sont-ils bien nommés (sans préfixes inutiles) ?  
- Les getters et setters correspondent-ils à notre cas d'utilisation ?  
- Avez-vous transformé les fonctions appropriées en méthodes de la classe ?  
- Avez-vous vérifié si les méthodes qui ne modifient pas l'objet sont marquées `const` ?
:::

Pour pouvoir saisir les informations de plusieurs étudiants, nous allons écrire plusieurs fonctions pour gérer les user inputs.

3. Écrivez une fonction `int inputStudentID()` qui retourne l'ID de l'étudiant à partir d'une user input.

4. Écrivez une fonction `void inputStudentName(Student& student)` qui modifie le nom de `student` à partir d'une user input.

:::{note} `cin >>`
`cin >>` affecte uniquement les valeurs saisies au clavier jusqu'à rencontrer un espace.  
Par exemple, si vous tapez `Jean Pierre`, `cin >>` retournera uniquement `Jean`.  
Pour éviter les détails de lecture (*parsing*) de l'entrée, nous supposerons qu'un nom est un seul mot ou plusieurs mots écrits en UpperCamelCase.
:::

5. Recopiez la fonction suivante pour récupérer les notes d'un étudiant :
```{code} cpp
void inputScores(Student& student) {
    double score;
    do {
        cout << "Enter score (-1 to stop): ";
        cin >> score;
        if (score != -1) {
            student.appendScore(score);
        }
    } while (score != -1);
}
```

6. Écrivez une fonction `Student createStudentFromInput()` qui crée un `Student` à partir des user inputs.

Nous pouvons maintenant créer un étudiant.  
Pour créer plusieurs étudiants et afficher toutes leurs notes ainsi que leurs moyennes, nous allons implémenter les fonctions suivantes.

7. Écrivez une fonction `int inputNumberOfStudents()` qui retourne le nombre d'étudiants à créer à partir d'une user input.

8. Écrivez une fonction `void printAllScoresAndAverages(const vector<Student>& students)` qui affiche les notes et moyennes de chaque étudiant de `students`.

9. Modifiez la fonction `main` pour gérer les user inputs grâce à toutes les fonctions que vous avons implémentées.

## Exercice 2 : Short functions

```{code} cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

using namespace std;

class Product {
private:
    string mName;
    double mPrice; // in euros
    int mQuantity; // in units

public:
    Product(string name, double price, int quantity) : mName(name), mPrice(price), mQuantity(quantity) {}

    string getName() const { 
        return mName; 
    }

    double getPrice() const { 
        return mPrice; 
    }

    int getQuantity() const { 
        return mQuantity; 
    }
};

void displayAvailableProductsByNonDecreasingPriceAndDisplayTotalPrice() {
    vector<Product> products;
    // Our current products
    products.push_back(Product("Laptop", 1000, 5));
    products.push_back(Product("Smartphone", 700, 0));
    products.push_back(Product("Tablet", 500, 2));
    products.push_back(Product("Headphones", 150, 10));
    products.push_back(Product("Smartwatch", 200, 0));

    // Getting available products
    vector<Product> availableProducts;
    for (const Product& product : products) {
        if (product.getQuantity() > 0) {
            availableProducts.push_back(product);
        }
    }

    // Sorting available products by non-decreasing price
    sort(availableProducts.begin(), availableProducts.end(), [](const Product& leftHandSideProduct, const Product& rightHandSideProduct){return leftHandSideProduct.getPrice() < rightHandSideProduct.getPrice();});

    // Calculating the total price of available products
    double totalPrice = 0;
    for (const Product& product : availableProducts) {
        totalPrice += product.getPrice() * product.getQuantity();
    }

    // Output results
    cout << "Sorted Available Products (by non-decreasing price):" << endl;
    for (const Product& product : availableProducts) {
        cout << "Name: " << product.getName() 
             << " - Price: " << product.getPrice() 
             << " - Quantity: " << product.getQuantity() << endl;
    }
    cout << "Total Price of Available Products: " << totalPrice << endl;
}

int main() {
    displayAvailableProductsByNonDecreasingPriceAndDisplayTotalPrice();
    return 0;
}
```

1. Que fait ce code ?

:::{note} `sort`
La fonction `sort` trie un `vector` en place (aucune copie du `vector` n'est créée).  
Elle prend en argument l'indice de début et de fin du `vector` ainsi que la comparaison `<` par défaut.  
Elle trie donc les valeurs de manière croissante (*non-decreasing* en anglais, *increasing* désignant un ordre strictement croissant).  
Lorsque le type de l'objet à trier est plus complexe, elle nécessite une fonction de comparaison en argument.  
Ici, la fonction est écrite sous la forme suivante :  
```cpp
[](const Product& leftHandSideProduct, const Product& rightHandSideProduct){return leftHandSideProduct.getPrice() < rightHandSideProduct.getPrice();}
```
Cela s'appelle une *fonction lambda*, ou *fonction anonyme* (sans nom).  

Nous allons éviter les fonctions anonymes en refactorisant la fonction de comparaison ailleurs sous la forme suivante :  
```cpp
bool <nom de la fonction de comparaison>(const Product& leftHandSideProduct, const Product& rightHandSideProduct){
    return leftHandSideProduct.getPrice() < rightHandSideProduct.getPrice();
}
```
Ensuite, nous pouvons appeler `sort` avec :  
```cpp
sort(availableProducts.begin(), availableProducts.end(), <nom de la fonction de comparaison>);
```
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

3. Refactorisez le code.

:::{hint} Avez-vous accompli votre tâche ?
:class: dropdown
- Avez-vous respecté **The Stepdown Rule** ?  
- Vos fonctions sont-elles bien nommées ?  
- Vos fonctions ont-elles une responsabilité unique ?  
- Vos fonctions ont-elles peu d'arguments ?  
- Vos fonctions avec un argument sont-elles bien classées dans les catégories vues en cours ?  
- Avez-vous placé `const` et `&` aux bons endroits ?  
:::

Les produits courants sont actuellement codés en dur.  
Nous pouvons ajouter des user input pour permettre l'entrée des informations sur les produits courants.  
La question suivante est un bonus.

4. **Bonus** : Implémentez les user inputs pour récupérer les informations sur les produits.

:::{note} `cin >>`
:class: dropdown
Comme précédemment, nous allons supposer qu'un nom est écrit en UpperCamelCase ou qu'il s'agit d'un seul mot.
:::

Revenez aux [objectifs](#tp3-objectifs) et cochez les points que vous avez maîtrisés. Revenez sur les points que vous n'avez pas encore bien compris. Appelez votre encadrant si besoin.