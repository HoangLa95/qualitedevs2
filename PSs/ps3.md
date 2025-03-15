# PS3: Introduction to refactoring

:::{important} Last Git reminder for this session
:class: dropdown
Maintain your repositories!
:::

:::{important} Conflict resolution
:class: dropdown
You have been introduced to conflict resolution in the SAÉ project. Try to resolve the conflicts on your own. If you encounter difficulty, ask for assistance from your instructor.
:::

:::{important} Ignore error handling for now
:class: dropdown
We have not covered error handling in class yet. For now, you can assume that the arguments are always valid and do not need to worry about error handling.
:::

:::{important} Compile and run regularly
:class: dropdown
Do not forget to compile and run your code regularly during the refactoring process. The behavior of your code should remain consistent with your expectations!
:::

:::{important} Code organization
In this session, we will learn to reorganize the code. The goal is no longer to create a single `.cpp` file containing all the code. Follow the instructions to properly organize the files and code.
:::

:::{important} Using an IDE
The code is becoming increasingly complex.  
A good syntax highlighting, autocompletion, and "Search and Replace" can save you a lot of time.  
Therefore, use an IDE (like VSCodium) without its Git integration for now.

Some useful things to know about VSCodium:
- Open the Git directory (the root of your repository) via **File** > **Open Folder**, and stay on **Explorer** (first icon in the left bar).
- You can create files and folders using the buttons located to the right of the root folder name.
- Right-clicking on a file opens a terminal in the directory of that file with **Open in Integrated Terminal**.
- Use {kbd}`Ctrl` + {kbd}`H` on a selection of code to perform a **Search and Replace** in the current file.
:::


(ps3-objectives)=
## Objectives

The goal of this session is to understand the following points:
- [ ] [Code organization and structuring](#ps3-code-organization)
- [ ] [Single Responsibility Principle](#cm3-unique-responsibility)
- [ ] [The Stepdown Rule](#cm3-stepdown-rule)
- [ ] [Functions with few arguments](#cm3-peu-arguments)
- [ ] [`const` and `&`](#ps3-const-and-ref)

## Exercise 1 : Short functions

### Code organization

1. Create a `short-functions/` directory inside `PS3/`.

2. Create the following files inside `short-functions/`.

::::{tab-set}
:::{tab-item} product.h
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
:::
:::{tab-item} product.cpp
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
:::
:::{tab-item} display-prices.h
```{code} cpp
#ifndef DISPLAY_PRICE_H
#define DISPLAY_PRICE_H

#include "product.h"

void displayAvailableProductsByNonDecreasingPriceAndDisplayTotalPrice();

#endif
```
:::
:::{tab-item} display-prices.cpp
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
:::
:::{tab-item} main.cpp
```{code} cpp
#include "display-prices.h"

int main() {
    displayAvailableProductsByNonDecreasingPriceAndDisplayTotalPrice();
    return 0;
}
```
:::
::::

:::{important} Why not group all the code into a single file?
:class: dropdown
When the project becomes more complex than a simple coding exercise, code separation brings the same benefits as the cleanliness and development principles discussed in class: modularity, reusability, maintainability, extensibility, encapsulation, readability, error handling, ...
:::

:::{important} Where is `using namespace std`?
:class: dropdown
`using namespace std` avoids the need to write `std::` before all types, functions, or objects from the C++ standard library.  
However, when the project becomes more complex, conflicts may arise if a function (or type/object) shares the same name as a function from the standard library.  
We will therefore avoid using `using namespace std` from now on.  
:::

:::{important} Header files `.h` in C++
:class: dropdown
In C++, header files (`.h`) contain the declarations of functions, classes, and variables, but not their definitions.  
This separation between declaration and definition allows C++ to manage dependencies and modularize the code.  
For example, if a source file `.cpp` needs the `Product` object, it is enough to include (`#include`) `"product.h"` without worrying about the implementation, because `product.h` "promises" that the declared attributes, constructor, and methods will be implemented.  
:::

:::{important} Preprocessor directives in C++
:class: dropdown
This part is called directives:
```{code} cpp
#ifndef PRODUCT_H
#define PRODUCT_H

#include <string>

//...

#endif
```
:::

:::{note} Compilation in C++
:class: dropdown
To properly organize C++ code and understand its importance, it is essential to grasp how compilation works in C++.  
In reality, the term "compilation" encompasses 4 steps for each source file `.cpp`.  

Preprocessing
: **Directives** are executed. For example, `#include` is replaced with the contents of the included file.  

Translation Unit
: The preprocessed file becomes a large text file called a translation unit.  

Compilation
: The translation unit is compiled into machine code in an object file (`.o`).  

Linking
: The object files are linked based on their dependencies (headers and corresponding source files) to form the executable.  
:::

:::{important} Why separate headers and source code in C++?
:class: dropdown
- Imagine a source code that contains both the declarations and the definition (which we are used to) of a class that we need to reuse in several files to build our executable.  
- The best practice is to compile all `.cpp` files, rather than trying to follow the dependencies between files and risk getting lost to avoid compiling everything.  
- This code will thus be compiled at least twice: first as a `.cpp` file and another time when included in another `.cpp` file.  
- C++ follows the **one definition rule**, which states that for a given executable, if the scopes of two variables or functions overlap, they must be different.  
- During linking, C++ detects an error because the executable contains duplicate definitions from different translation units.  
- A header is not compiled like a source file because it only contains declarations, not the definitions themselves. When the header is included in source files `.cpp` via `#include`, identical declarations may appear multiple times, but **C++ allows multiple declarations of the same element with different scopes or from different translation units** (as long as there is only one definition).  
- Therefore, **never include a `.cpp`, only `.h`** files.
:::

:::{important} Include guards
:class: dropdown
These directives together form an **include guard** :
```{code} cpp
#ifndef PRODUCT_H
#define PRODUCT_H

//...

#endif
```
- `#define` creates a **macro** that replaces the defined text (`PRODUCT_H`) with the following code. The naming convention for macros is the same as for global constants and includes `_H` to indicate that it is a macro related to the include guard of a header (though other types of macros may exist).  
- `#ifndef` (short for "if not defined"), `#define`, and `#endif` mean: "If the macro PRODUCT_H is not defined, define the macro PRODUCT_H as follows: `<code>`, end of the condition".  

The include guard ensures that a header is included only once in each source code and therefore in each translation unit.  

The downside of include guards is the need for a consistent naming scheme for an executable (whose compilation can come from thousands of different files spread across different directories): there cannot be two headers with the same macro (even if the files are in very distant directories from each other).  

Another directive is sometimes used instead of include guards, but it also has its own drawbacks.  
Standard best practices in C++ recommend using include guards.  
:::

:::{important} Why include `<string>` a "second" time?
:class: dropdown
If `product.h` includes `<string>` and `product.cpp` includes `product.h`, why do we need to include `<string>` a second time?  
**Include what you see** is a good practice in C++.

First, headers do not always contain all the libraries needed for source files, as some libraries may not be necessary for a declaration but will be essential for the definition.  
Additionally, checking that all necessary libraries are already included in the headers is a waste of time when there are many headers. On the other hand, an extra include is not an issue if include guards are used (which is the case for all C++ libraries).
:::

:::{important} `Product::`
:class: dropdown
It is necessary to write `Product::` in front of all methods of `Product` to indicate to C++ that these elements are internal to `Product`. It is possible to define functions external to `Product` in the same file, but this is not a good practice.
:::

:::{important} `operator<`
:class: dropdown
The method `operator<` is an **override** of the default comparison `<`.  
This means that `Product` objects cannot be compared with `<` in the traditional way. We redefine this comparison by saying that one `Product` object is smaller than another (`other`) if its price is smaller (`mPrice < other.mPrice`). 
This will allow sorting the products based on their price later.
:::

:::{note} `for (const Product& product : products)`
:class: dropdown
It is possible to write a `for` loop with the following syntax: `for (ElementType element : vectorOfElements)`.
:::

:::{important} Code organization
:class: dropdown
It is good practice to break down the code into classes, related external functions, and the main (each having its own header, except for `main`).  
Finer breakdowns of external functions can be made if part of these functions needs to be reused elsewhere (modularity and reusability).
:::

3. Compile the code with `g++ -o short-functions main.cpp display-prices.cpp product.cpp` (the order of `.cpp` files does not matter).

:::{note} C++ version
:class: dropdown
If you see warnings during compilation related to the syntax standards used (for example, `for (ElementType element : vectorOfElements)`, which only exists since C++11), you can compile with a more recent version of C++ that is compatible with your `g++` compiler.

To check the version (year) of C++ used by default by `g++` on your workstation, use the following command:
```{code} sh
g++ -dM -E -x c++ /dev/null | grep __cplusplus | sed 's/[^0-9]*\([0-9]\{4\}\)[0-9]*L/\1/'
```
For example, `2011` corresponds to C++11.

To check the versions compatible with `g++` on your workstation, use the command:
```{code} sh
g++ -v --help 2> /dev/null | grep -oP '(?<=-std=)c\+\+\S+' | sed 's/\.$//' | sort | uniq
```
The latest C++ versions after 11 are C++14, 17, 20, and 23.

You can compile your code using a more recent and compatible version than the default one. For example:
```{code} sh
g++ -std=c++14 -o short-functions main.cpp display-prices.cpp product.cpp
```
:::

4. Run `./short-functions`.

(ps3-code-organization)=
:::{important} Summary of good code organization practices in C++
- Do not put everything in a single file.  
- Use a `.cpp` file and a corresponding `.h` file for each class and each group of related external functions, and then a `main.cpp` file.  
- A `.cpp` and `.h` file for a class should carry the name of the class, and a `.cpp` and `.h` file for external functions should carry the name that links them.  
- `.h` files should contain only declarations, and `.cpp` files should contain definitions.  
- `.h` files must always have **include guards**, i.e.:  
```cpp
#ifndef HEADER_NAME_H
#define HEADER_NAME_H

//...

#endif
```  
- Do not forget to use `MyClass::` in front of methods in `my-class.cpp` that define the class `MyClass` declared in `my-class.h`.  
- If you need the elements defined in a `code.cpp` file, then `#include "code.h"` (which declared those elements), not `code.cpp` itself.  
- **Include what you see**: Add the libraries and headers you use for your code at the beginning, even if a header you include already contains them.  
- No longer use `using namespace std`.  
:::

:::{warning} Java and C#
- In Java and C#, there is no separation between headers and source code files, as their compilation systems are different. Code elements are both declared and defined in the same file.
- However, it remains essential to organize the code into multiple files, just like in C++.
- In Java and C#, it is necessary to name a file containing a class after the class itself (like in C++), but using the **UpperCamelCase** convention (also called **PascalCase**). Additionally, in Java, compilation will fail if the file containing a class is not correctly named, unlike in C++.
- In Java and C#, the principle of **Include what you use** is followed, meaning that only what is necessary for the code to function properly is imported (`import` in Java, `using` in C#). This differs from the **Include what you see** principle in C++, which relies on include guards to prevent code duplication.
:::

### Refactoring the code

1. What does this code do?

:::{note} `sort`
:class: dropdown
The `sort` function sorts a `vector` in place (no copy of the `vector` is created). It takes as arguments the start and end indices of the `vector` and uses the default `<` comparison (which has been redefined for `Product`). Therefore, it sorts the values in a non-decreasing order (*increasing* meaning strictly ascending).
:::

(ps3-const-and-ref)=
:::{hint} `const` and `&`
:class: dropdown
Using `const` for function arguments ensures that the function will not modify the `const` argument. The reference `&` for arguments allows working on the same object as the one passed to the function, avoiding the creation of a copy of the object. This also helps avoid copying "heavy" objects. Here, `Product` has three attributes of type `string`, `double`, and `int`. `double` and `int` are considered "light", while `string` can be "heavy".
:::

2. **Quiz**: What are the issues with the code of `displayAvailableProductsByNonDecreasingPriceAndDisplayTotalPrice`?

3. Refactor the code of `display-prices.cpp` (by modifying the other affected files, such as the header and main).

:::{hint} Did you accomplish your task?
:class: dropdown
- Did you follow **The Stepdown Rule**?  
- Are your functions well-named?  
- Do your functions have a single responsibility?  
- Do your functions have few arguments?  
- Are your one-argument functions categorized as discussed in class?  
- Did you place `const` and `&` in the correct places?
:::

:::{note} Hardcoded information
We will ignore the fact that product information is hardcoded. Let's imagine that this information comes from a database.
:::

## Exercise 2: Classify it

1. Create a directory `classify-it/` (within which the code will be organized into several different files).

2. In `classify-it/`, create the following file.

```{code} cpp
:filename: main.cpp
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
    std::cout << std::endl << "Scores for " << studentName << " (Id: " << studentId << "): ";
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

2. What does this code do?

:::{note} `size_t`
:class: dropdown
`size_t` is the default type for the index of a `vector` rather than `int` because a `vector` can be very large, and its indices might exceed the limit of `int`.
:::

It is difficult to add students and their grades.  
We are therefore going to create a `Student` class with the following three attributes:  
- Only the student’s Id is necessary to construct the object. The Id is **immutable**: once initialized by the constructor, it cannot be modified. This is made possible by using `const` in front of the attribute.

:::{note} Initializing a `const` attribute
:class: dropdown
An immutable (`const`) attribute can only be initialized in the **initializer list** in C++.  
For example:
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
This attribute cannot be initialized in the constructor body.
For example, the following code will result in a compilation error:
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

- The student's name can be modified. Initially, it is empty (by default, an uninitialized `string someString` is `== ""`). We will not handle potential errors from an empty name in this exercise.

:::{hint} Setter
:class: dropdown
A typical setter looks like this:
```{code} cpp
void setAttribute(const AttributeType& valueName){
    mAttribute = valueName;
}
```
:::

- The student's `vector` of scores will not be accessible. The only access will be through displaying (`printScores`) and adding scores via the `appendScore` function, which adds a score to the end of the `vector` using `push_back`. There are no scores initially, but we will also ignore potential errors coming from an empty `vector` in this exercise.

3. Create the `Student` class with the appropriate methods (including the corresponding `.h` and `.cpp` files), and add functions to calculate the average and display the scores.

4. Modify the `main.cpp` directives to include `student.h` and the necessary libraries.

:::{hint} Did you accomplish your task?
:class: dropdown
- Do the `.h` and `.cpp` files follow best practices?
- Are the attributes named properly (without unnecessary prefixes)?
- Do the getters and setters match our use case?
- Have you converted the functions into methods of the class properly?
- Have you checked if methods that do not modify the object are marked `const`?
:::


### User input (Bonus)

The benefit of having a `Student` class is to make it easier to enter data for multiple students.  
In order to input data for several students, we will write several functions to handle user input.

1. Create a file `user-input.cpp` along with the associated header file.

2. Here are some function declarations to help you:
```{code} cpp
void inputNumberOfStudents(int& number);
void inputStudentId(int& id);
void inputStudentName(Student& student);
void inputStudentScores(Student& student);
Student createStudentFromInput();
void printAllScoresAndAverages(const std::vector<Student>& students);
```

3. Here is the code of `inputStudentScores` to help you:
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
`cin >>` affects only the values typed on the keyboard until it encounters a space.  
For example, if you type `Jean Pierre`, `cin >>` will return only `Jean`.  
To avoid the details of input parsing, we will assume that a name is either a single word or several words written in UpperCamelCase.
:::

4. Implement the given functions and modify `main` to retrieve user input and display the grades as well as the averages for each student.

Return to the [objectives](#ps3-objectives) and check the points you have mastered. Review the points you have not yet fully understood. Ask your instructor for help if needed.
