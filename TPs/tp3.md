# TP3 : Initiation à la refactorisation

:::{important} Dernier rappel Git de ce TP
Maintenez votre dépôt !  
:::

:::{important} Résolution de conflits
:class: dropdown
Vous avez vu une initiation à la résolution de conflits en Projet SAÉ. 
Essayez de résoudre les conflits vous-même.
En cas de difficulté, appelez votre encadrant.
:::

:::{important} Gestion des erreurs
:class: dropdown
Nous n'avons pas encore abordé la gestion des erreurs en classe.
Pour le moment, vous pouvez supposer que les arguments sont toujours valide sans vous soucier de la gestion des erreurs.
:::

:::{important} Tests
Il ne faut pas oublier de tester votre code régulièrement quand vous faites de la refactorisation. Il faut que les résultats retournés soient cohérents !
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

Il est difficile de rajouter des étudiants et leurs notes.
Nous allons donc créer une classe `Student` avec les trois attributs :
- Nous allons considérer qu'il suffit d'avoir l'ID d'un étudiant pour construire l'objet.
- L'ID est *immutable* : une fois qu'elle est initialisée par le constructeur, elle ne pourra pas être modifiée. Ceci est possible grâce à `const` devant l'attribut.
- Il est possible de modifier le nom d'un objet étudiant (qui peut être créé sans nom).
- Nous ne voulons pas donner accès au `vector` des notes de l'étudiant à part l'affichage (`printScores`) et pour ajouter des notes à travers une fonction `appendScore` qui ajoute une note à la fin du `vector` grâce à `push_back`.

:::{hint} Setter
:class: dropdown
Un setter typique ressemble au suivant :
```{code} cpp
void setAttribute(const AttributeType& valueName){
    mAttribute = valueName;
}
```
:::

2. Créez la classe `Student`.

:::{hint} Avez-vous accompli votre tâche ?
:class: dropdown
- Est-ce que les attributs sont bien nommés (pas de préfixes inutiles) ?
- Est-ce que les getters et setters correspondent à notre cas d'utilisation ?
- Est-ce que vous avez transformé les fonctions appropriées en méthodes de la classe ?
- Est-ce que vous avez vérifier si les méthodes qui ne modifie pas l'objet sont `const` ?
:::

Pour pouvoir rentrer les informations de plusieurs étudiants, nous allons écrire plusieurs fonctions pour gérér les user inputs.

3. Écrivez une fonction `int inputStudentID()` qui retourne l'ID de l'étudiant à partir d'un user input.

4. Écrivez une fonction `void inputStudentName(Student& student)` qui modifie le nom de `student` à partir d'un user input.

:::{note} `cin >>`
`cin >>` va juste affecter les valeurs rentrées au clavier jusqu'à trouver un espace.
Par exemple, si vous tapez `Jean Pierre` alors `cin >>` va juste retourner `Jean`.
Pour éviter les détails de parsing d'entrée, nous allons supposer qu'un nom est un seul mot ou est plusieurs mots écrits en UpperCamelCase.
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

6. Écrivez une fonction `Student createStudentFromInput()` qui crée un objet étudiant à partir des user inputs.

Nous pouvons maintenant créer un étudiant. 
Pour créer plusieurs étudiants et afficher tous leurs notes et moyennes, nous allons implémenter les fonctions suivantes.

7. Écrivez une fonction `int inputNumberOfStudents()` qui retourne le nombre d'étudiants à créer à partir d'un user input.

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
La fonction `sort` trie un `vector` en place (pas de copie de `vector` créée).
Elle prend en argument l'indice du début et de la fin du `vector` et la comparaison `<` par défaut.
Elle trie donc les valeurs de façon croissante (*non-decreasing* en anglais, *increasing* est "strictement croissant*).
Quand le type de l'objet à trier est plus complexe, elle demande une fonction de comparaison en argument.
Ici, la fonction est écrite sous la forme :
```{code} cpp
[](const Product& leftHandSideProduct, const Product& rightHandSideProduct){return leftHandSideProduct.getPrice() < rightHandSideProduct.getPrice();}
```
Ceci est appelé une *fonction lambda*, ou *fonction anonyme* (sans nom).

Nous allons éviter les fonctions anonyme en refactorisant la fonction de comparaison ailleurs sous la forme suivante :
```{code} cpp
bool <nom de la fonction de comparaison>(const Product& leftHandSideProduct, const Product& rightHandSideProduct){
    return leftHandSideProduct.getPrice() < rightHandSideProduct.getPrice();
}
```
Puis, nous pouvons appeler `sort` avec :
```{code} cpp
sort(availableProducts.begin(), availableProducts.end(), <nom de la fonction de comparaison>);
```
:::

(tp3-const-and-ref)=
:::{hint} `const` et `&`
:class: dropdown
L'utilisation de `const` pour les arguments des fonctions promets que la fonction ne va pas modifier l'argument `const`.
La référence `&` pour les arguments permet de travailler sur le même objet que quand la fonction est appelé et ne pas créer de copie de l'objet.
Cela permet aussi d'éviter de copie les objets "lourds".
Ici, `Product` a trois attributs de type `string`, `double` et `int`.
`double` et `int` sont considérées comme "léger" alors que `string` peut être "lourd".
:::

2. **Quiz** : Quels sont les problèmes de ce code ?

3. Refactoriser le code.

:::{hint} Avez-vous accompli votre tâche ?
:class: dropdown
- Est-ce que vous avez respecté **The Stepdown Rule** ?
- Est-ce que vos fonctions sont bien nommées ?
- Est-ce que vos fonctions ont une responsabilité unique ?
- Est-ce que vos fonctions ont peu d'arguments ?
- Est-ce que vos fonctions à un argument rentrent bien dans les catégories vues en cours ?
- Est-ce que vous avez mis `const` et `&` aux bons endroits ?
:::

Les produits courants apparaissent en dur dans le code. 
Nous pouvons ajouter des user inputs pour pouvoir rentrer les informations sur les produits courants.
Ceci est un bonus.

4. **Bonus** : Implémenter les user inputs pour les informations sur les produits.

:::{note} `cin >>`
Comme avant, nous allons considérer qu'un nom est écrit en UpperCamelCase ou est un seul mot.
:::