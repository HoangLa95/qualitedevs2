# TP3 : Initiation à la refactorisation

## Exercice 1 : Short functions

```{code} cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

using namespace std;

class Product {
private:
    string mName;
    double mPrice;
    int mQuantity;

public:
    Product(string name, double price, int quantity) 
        : mName(name), mPrice(price), mQuantity(quantity) {}

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

## Exercice 2 : Classify it

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

void printScores(const string& studentName, int studentID, const vector<double>& scores) {
    cout << endl << "Scores for " << studentName << " (ID: " << studentID << "): ";
    for (size_t i = 0; i < scores.size(); i++) {
        cout << "[" << i+1 << "] " << scores[i] << " ";
    }
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

:::{important} Tests
Il ne faut pas oublier de tester votre code régulièrement quand vous faites de la refactorisation. Il faut que les résultats retournés soient cohérents !
:::
