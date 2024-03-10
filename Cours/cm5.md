# Comment gérer les erreurs ?

```{figure} ../images/error-meme.jpg
:alt: Gestion des erreurs
:align: center

Si tout le code est dans une seule procédure, alors on a besoin d'un seul gestionnaire d'erreurs (Credits: Mike Wolfe, No Longet Set).
```
:::{danger} Si ce n'est pas clair, ce meme n'est pas un principe de code propre !
:class: dropdown
- Mon collègue : Combien est-tu prêt à parier que les étudiants suivront ce "conseil" pour gérer les erreurs ?
- Moi : ... *ajoute un bloc "danger" dans le cours*
:::

Il y a plusieurs types d'erreurs :
- **Erreur à la compilation** (cf. votre cours d'Anglais et votre ami Google).
- **Erreur à l'éxécution**.
    - **Erreur côté développeur** (cf. TP5 et votre cours d'IHM).
    - **Erreur côté utilisateur** (ce cours).

## try, throw, catch

**Syntaxe d'un gestionnaire d'erreurs** :
```{code} cpp
try {
  //Code to try
  //...
  
  //Throw an exception when encoutering user error
  throw(exception); 
}
catch (ExceptionType exception) {
  // Code to handle errors
}
```

Example :
```{code} cpp
try {
  int age = 17;
  if (age >=18)
    cout << "Access granted" << endl;
  else
    throw(age); 
}
catch (int age) {
  cerr << "Access denied: you must be at least 18 years old." << endl;
}
```

:::{danger} `try` et `catch` sans `throw`
:class: dropdown
Il est possible de faire `try` et `catch` sans `throw` mais c'est très dangereux ! Vous risquez d'attraper une erreur qui n'est pas prévue. Dans ce cas, le comportement du code peut être obfusqué (on pense qu'une erreur prévue a été levée alors que c'est une erreur imprévue) et cela rend le code encore plus difficile à débugger.
:::

## Programmation robuste

:::{note} Robustesse
:class: dropdown
La robustesse d'un programme est sa capacité de gérer des erreurs et de permettre aux utilisateurs et développeurs d'identifier les problèmes et de les corriger. La robustesse concerne aussi la conception du programme et non seulement l'écriture des gestionnaires d'erreurs.
:::

Les principes de la programmation robuste:
- **Être paranoïaque** : L'utilisateur est là pour casser notre code, il faut anticiper ses actions.
- **Ils sont stupides** : Il faut écrire les messages d'erreur de telle sorte que l'utilisateur puisse corriger les erreurs lui-même.
- **Ils sont dangereux** : Il ne faut pas laisser l'utilisateur manipuler le code source. Autrement dit, il faut qu'il puisse faire ce dont il a besoin juste avec le code que l'on lui a fournit.
- **Rien n'est impossible** : Un code peut évoluer. Une erreur que l'on pense être impossible peut arriver.

## (Don't) catch everything

Il nous arrive souvent de devoir définir plusieurs blocs `catch` pour les différents types d'erreurs.

```{code} cpp
#include <iostream>
#include <stdexcept>

using namespace std;

void performOperation(int value) {
    try {
        if (value < 0) 
            throw invalid_argument("Negative value");
        if (value == 0) 
            throw runtime_error("Zero value"); 
        cout << "Performing operation with value: " << value << endl;
    } catch (invalid_argument& e) {
        cerr << "Invalid argument error caught: " << e.what() << endl;
    } catch (runtime_error& e) {
        cerr << "Runtime error caught: " << e.what() << endl;
    }
}
```

Ou nous pouvons aussi attraper toutes les erreurs dans un seul bloc `catch` quand ils ont toujours le même comportement.

```{code} cpp
#include <iostream>
#include <stdexcept>

using namespace std;

void performOperation(int value) {
    try {
        if (value < 0) 
            throw invalid_argument("Negative value");
        if (value == 0) 
            throw runtime_error("Zero value"); 
        cout << "Performing operation with value: " << value << endl;
    } catch (...) {
        cerr << "Error caught: " << e.what() << endl;
    }
}
```
:::{warning} (Don't) catch everything!
:class: dropdown
Il est important de gérer toutes les erreurs mais il faut faire attention quand vous faites `catch(...)`. 

Il y a toujours le risque d'attraper une erreur non-prévue même si le message d'erreur permettrait de voir la différence entre une erreur prévue et une erreur non-prévue.

Le plus gros risque ici est d'attraper une erreur de compilation et de perdre les informations utiles (comme l'endroit où l'erreur s'est produite) qui nous aide à débugger le code.

Est-ce que ça veut dire qu'il ne faut jamais utiliser `catch(...)` ? Ça dépend ! `catch(...)` permet aussi d'éviter les redondances dans le code. Si le risque d'attraper une erreur de compilation est aussi grand que des pavés de code copiés collés alors il faut peut-être structurer les erreurs dont la gestion est la même sous une même classe ou repenser la structure de votre code.
:::

:::{important} Commencer par écrire le gestionnaire d'erreur.
:class: dropdown
Nous allons apprendre à écrire des **tests unitaires** dans le prochain cours. Il faut donc anticiper ces erreurs et écrire des tests avant de commencer à coder (principe du **Test Driven Development**).

Les blocs `try-catch` sont nécessaires pour la gestion des erreurs utilisateurs. Vu leur nécessité et vu qu'ils dictent le *flot du code* (le code peut être interrompu pour rentrer dans `catch`, la gestion de l'exception), il faut commencer par mettre en place les blocs `try-catch` et suivre la structure imposée par le gestionnaire d'erreur.

:::{danger} Ce principe est pour gérer les erreurs d'utilisateur, pas les erreurs de compilations !
Rappel de l'importance d'utiliser `throw` avec le `catch` correspondant et le danger d'utiliser `catch(...)`.
:::
:::


Consulter [C++ exceptions standard](https://en.cppreference.com/w/cpp/error/exception).

## Gestion des erreurs dans Java

Votre cours de Développement Orienté Objet est en Java donc voici quelques principes de gestions d'erreurs qui sont liés à Java et le DOO.

:::{important} Ne pas utiliser des Checked Exceptions
:class: dropdown
Dans Java, il y a un concept de **checked exceptions** où nous pouvons gérer des erreurs de compilation. Cela requiert la déclaration du type d'erreur levé dans la signature de la fonction.

Il y a une raison pourquoi ce concept n'existe pas en C++ qui permet quand même d'écrire des programmes robustes. Imaginons que vous avez écrit des fonctions plus abstraites qui appellent des fonctions de plus en plus concrètes et lors de la fonction de plus bas niveau, vous avez une *checked exception*, il faudra alourdir le code en rajoutant dans la signature de toutes les fonctions précédentes le type de l'exception qui peut être levée.

Je ne vais même pas vous montrer un exemple de ça.
:::

```{code} java
public void registerItem(Item item) {
    if (item != null) {
        ItemRegistry registry = peristentStore.getItemRegistry();
        if (registry != null) {
            Item existing = registry.getItem(item.getID());
            if (existing.getBillingPeriod().hasRetailOwner()) {
                existing.register(item);
            }
        }
    }
}
```

:::{danger} `null`, c'est nul !
:class: dropdown
Il ne faut pas autoriser des fonctions à retourner `null` ! Vous allez passer votre temps à vérifier si un objet est `null`. Cela rend le code illisible et si vous oubliez de vérifier si un objet est `null`, plus rien ne marche. **Est-ce que vous avez remarqué que l'on n'a pas vérifié si `existing` est `null` ?**
:::

## Special Case Pattern

:::{important} Disclaimer
:class: dropdown
Le code suivant est en Java parce que nous allons utiliser le concept d'**interface** que vous allez voir en programmation objet (cours en Java). Nous n'allons pas rentrer dans les détails de la notion d'interface, juste dans la façon dont ça pourrait être utilisée pour gérer les cas spéciaux dans un code.
:::

```{code} java
try {
    MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
    total += expenses.getTotal();
} catch(MealExpensesNotFound e) {
    total += getMealPerDiem();
}
```

:::{important} Gestion d'un cas spécial
:class: dropdown
Dans le code précédent, un employé part en mission et lors de son retour, on vérifie ses dépenses en repas. Si on ne trouve pas de dépenses, on lève une exception et rajoute la somme allouée par jour au total.

La présence de `try-catch` ici n'est pas nécessaire pour la logique du code parce que c'est plus un cas spécial qu'une erreur. Dans ce cas, on voudrait bien simplifier le code de la façon suivante.
```{code} java
MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
total += expenses.getTotal();
```
Il faut juste faire en sorte que `expenseReportDAO.getMeals()` retourne toujours un objet `MealExpenses`.
:::

**Solution**:

```{code} java
public class PerDiemMealExpenses implements MealExpenses {
    public int getTotal() {
        // return the per diem default
    }
}

MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
total += expenses.getTotal();
```
