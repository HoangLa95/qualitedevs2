# Comment gérer les erreurs (utilisateurs) ?

D'abord, un point sur Git.

## Structure de donnée de l'historique Git

:::{hint} Indice : je fais de la recherche en ... 
:class:dropdown
```{figure} ../images/git-graph.png
:alt: Git Graph
:width: 50%
:align: center

Credits: [Marco Eidinger](https://blog.eidinger.info/the-easiest-way-to-draw-git-graphs)
```

C'est un DAG (Directed Acyclic Graph) où :
- les sommets sont des commits,
- un arc d'un commit `A` vers un commit `B` représente l'évolution d'un dépôt de l'état `A` vers l'état `B`,
- le graphe "évolue dans une direction" qui correspond au temps donc il est sans cycle dirigé.

Branchement :
- Les branches en Git correspond à des "branches" de ce graphe.
- Des conflits à cause de branches divergentes arrive quand un même dépôt se trouve dans deux états différents (`A` pointe à la fois vers `B` et `C`).
- Des `merge` fusionnent deux branches en une seule (`D` et `E` pointe tous les deux vers `F`).

Nous allons apprendre à naviguer ce graphe en TP !
:::

## Gestion des erreurs

:::{important} Quels sont les différentes catégories d'erreur ?
- Erreur à la compilation (cf. votre cours d'Anglais et Google).
- Erreur à l'éxécution.
    - Erreur côté développeur (cf. TP debugger en IHM).
    - **Erreur côté utilisateur** (ce cours).
:::

### Programmation robuste

:::{note} Robustesse
:class: dropdown
La robustesse d'un programme est sa capacité de gérer des erreurs et de permettre aux utilisateurs d'identifier les problèmes et de les corriger. La robustesse concerne aussi la conception du programme et non seulement l'écriture des gestionnaires d'erreurs.
:::

:::{important} Les principes de la programmation robuste  
- **Soyez paranoïaque** : L'utilisateur, volontairement ou non, mettra votre code à l'épreuve. Il est essentiel de prévoir ses actions pour éviter les comportements imprévus.
- **Ils sont stupides** : Les messages d'erreur doivent être clairs, bienveillants et fournir les informations nécessaires pour que l'utilisateur puisse corriger l'erreur lui-même : explication concise, lien vers la documentation si besoin, et suggestions de correction.
- **Ils sont dangereux** : L'utilisateur ne doit pas avoir à manipuler directement le code source. Il doit pouvoir accomplir ses tâches uniquement avec les fonctionnalités mises à sa disposition.
- **Rien n'est impossible** : Ne supposez jamais qu'une condition sera toujours respectée. Concevez votre code comme une machine, si une erreur peut techniquement arriver alors elle arrivera forcément.
:::

### Input validation

Pour certaines erreurs, il n'est pas nécessaire d'arrêter l'exécution du programme et il suffit de valider l'entrée de l'utilisateur avant de passer à l'étape suivante.

```{code} cpp
#include <iostream>
#include <limits> // for numeric_limits
#include <stdexcept>

int getPositiveInteger() {
    int userInput;
    int attempts = 0;
    const int maxAttempts = 5;

    while (attempts < maxAttempts) {
        std::cout << "Enter a positive integer: ";
        std::cin >> userInput;

        if (std::cin.fail() || userInput <= 0) { // cin.fail() is true if user inputs non-integer value or the value is too large
            std::cin.clear(); // cin.clear() resets the state of cin if it was in fail state
            std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n'); // Clear the buffer until it reaches end of line character '\n'.
            attempts++;
            std::cout << "Invalid input. Please try again. For example: 1" << std::endl;
            std::cout << maxAttempts - attempts << " left." << std::endl;
        } else {
            return userInput;
        }
    }
    
    // Explained below
    throw std::invalid_argument("Maximum attempts reached.");
}
```

### try, throw, catch

:::{important} Syntaxe d'un gestionnaire d'erreurs
```{code} cpp
#include <stdexcept> // for standard exceptions

try {
  //Code to try
  //...
  
  //Throw an exception when encoutering user error
  throw(std::exception("Error message")); 
} catch (const std::exception& error) {
  // Code to handle errors
  //...
}
```

Example :
```{code} cpp
#include <iostream>
#include <limits>
#include <stdexcept>
#include <cstdlib> // for exit

int getPositiveInteger() {
    //...

    throw std::invalid_argument("Maximum attempts reached.");
}

void processUserInput() {
    try {
        int number = getPositiveInteger();
        // ...

        // Example of another throw
        throw std::runtime_error("Some error message here.");

    } catch (const std::invalid_argument& error) {
        // Output the error message (error.what()) on the cerr stream (for errors)
        std::cerr << "Error: " << error.what() << std::endl; // Output: Error: Maximum attempts reached.
        exit(EXIT_FAILURE);
    } catch (const std::runtime_error& error) {
        std::cerr << "Error: " << error.what() << std::endl;
        // Possibly handles error gracefully without exiting
    }
}
```
:::

:::{important} Standard exception
:class: dropdown
Quelques exceptions utiles de la bibliothèque standard de C++:
`std::exception` est l'exception de base qui se dérive en :
- `std::logic_error` :
    - `std::invalid_argument` 
    - `std::out_of_range`
- `std::runtime_error` :
    - `std::overflow_error`

Consulter [C++ exceptions standard](https://en.cppreference.com/w/cpp/error/exception) pour beaucoup plus de types d'exceptions.

Soyez précis quand vous pouvez et évitez d'utiliser juste `std::exception`.
:::

:::{danger} Ne pas gérer les erreurs de développeurs !
:class: dropdown
Il est crucial de gérer les erreurs d'utilisateur, mais attraper des erreurs imprévues dans le code des développeurs peut compliquer le débogage. C'est pourquoi il est important d'utiliser `throw` avec un `catch` approprié, afin de ne pas capturer des erreurs inattendues.
:::

:::{danger} `null`, c'est nul !
:class: dropdown
Il faut éviter d'utiliser `nullptr` en C++ (`null` en Java et C#). Vous allez passer votre temps à vérifier si un objet est `nullptr` et cela rend le code illisible.

Exemple:
```{code} cpp
void registerItemInStore(Item* item, Store store) {
    if (item != nullptr) {
        ItemRegistry* registry = store.getItemRegistry();
        if (registry != nullptr) {
            Item* existing = registry->getItem(item->getID());
            if (existing->getBillingPeriod().hasRetailOwner()) {
                existing->register(item);
            }
        }
    }
}
```
Est-ce que vous avez remarqué que l'on n'a pas vérifié si `existing` est `nullptr` ?
:::
