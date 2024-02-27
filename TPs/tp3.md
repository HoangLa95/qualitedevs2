# TP3 : Initiation à la refactorisation

:::{important} QCM
:class: dropdown
Les réponses aux questions du TPs sont à remplir dans le QCM sur Moodle.
:::

:::{important} Git
:class: dropdown
Dernier rappel `git` de ce TP : `status`, `pull`, `.gitignore`, `add`, `commit`, `push`.
:::

:::{important} IDE
:class: dropdown
Dernier TP sans IDE ! J'espère que vous avez bien compris le workflow typique de Git !
:::

## Exercice 1 : Shape area

```{code} cpp
#include <iostream>
#include <cmath>

using namespace std;

double getArea(const string& shape, double width, double height, double radius) {
    if (shape == "circle")
        return M_PI * radius * radius;
    else if (shape == "square")
        return width * width;
    else if (shape == "rectangle")
        return width * height;
    else {
        cerr << "Invalid shape!" << endl;
        return -1;
    }
}

int main() {
    double circleArea = getArea("circle", 0, 0, 5);
    cout << "Area of circle: " << circleArea << endl;

    double squareArea = getArea("square", 4, 0, 0);
    cout << "Area of square: " << squareArea << endl;

    double rectangleArea = getArea("rectangle", 3, 5, 0);
    cout << "Area of rectangle: " << rectangleArea << endl;

    return 0;
}
```

**Question 1** : Quels sont les problèmes du code précédent ?

Pour améliorer ce code, nous allons créer une classe `Shape`.

**Question 2** : Combien d'attributs (privés) y-a-t-il dans `Shape` ?

Dans `public`, nous avons besoin d'écrire un constructeur pour `Shape` (exemple de constructeur dans l'[exercice 2 du TP2](#tp2-ex2-class-example)).

**Question 3** : Comment éviter d'utiliser les mêmes noms pour les arguments et les attributs de `Shape` ?

**Question 4** : Combien de méthodes (au moins) a-t-on besoin dans `Shape` (sans compter les getters et setters) ?

:::{important} Tests
:class: dropdown
Il ne faut pas oublier de tester votre code régulièrement quand vous faites de la refactorisation. Il faut que les résultats retournés soient les mêmes !
:::

## Exercice 2 : Magasin de qualité

Un magasin d'items pour aventuriers vend différents items dont la qualité diminue ou augmente plus on se rapproche de la date d'expiration. Ce magasin vient de changer de propriétaire. Le nouveau propriétaire vérifie le système informatique en charge du contrôle de qualité et ne comprend absolument rien à `updateItems` dans ce code.

```{code} cpp
#include <iostream>
#include <string>
#include <vector>

using namespace std;

class Item {
private:
    string mName;
    int mExpiresIn;
    int mQuality;

public:
    Item(string name, int expiresIn, int quality) : mName(name), mExpiresIn(expiresIn), mQuality(quality) {}

    string getName() {
    	return mName;
    }
    
    int getExpiresIn() {
    	return mExpiresIn;
    }
    
    int getQuality() {
    	return mQuality;
    }
    
    void setQuality(int quality) {
    	mQuality = quality;
    }
    
    void setExpiresIn(int expiresIn) {
    	mExpiresIn = expiresIn;
    }
};

class Shop {
private:
    vector<Item> & items;
    
public:
    Shop(vector<Item> & itemsInStock) : items(itemsInStock) {}
    
    vector<Item> getItems() {
    	return items;
    }
    
    void updateItems(){
    	for (int i = 0; i < items.size(); i++) {
    	    if (items[i].getName() != "Cheese" && items[i].getName() != "Concert Ticket") {
    	        if (items[i].getQuality() > 0) {
    	            if (items[i].getName() != "Excalibur") {
    	                items[i].setQuality(items[i].getQuality() - 1);
    	            }
    	        }
    	    } else {
    	        if (items[i].getQuality() < 20) {
    	            items[i].setQuality(items[i].getQuality() + 1);
    	            if (items[i].getName() == "Concert Ticket") {
    	            	items[i].setQuality(items[i].getQuality() + 1);
    	            }
    	        }
    	    }
    	    if (items[i].getName() != "Excalibur") {
    	        items[i].setExpiresIn(items[i].getExpiresIn() - 1);
    	    }
    	    if (items[i].getExpiresIn() < 0) {
    	        if (items[i].getName() != "Cheese") {
    	            if (items[i].getName() != "Concert Ticket"){
    	                if (items[i].getQuality() > 0){
    	                    if (items[i].getName() != "Excalibur") {
    	                        items[i].setQuality(items[i].getQuality() - 1);
    	                    }
    	                }
    	            } else {
    	                items[i].setQuality(0);
    	            }
    	        } else {
    	            if (items[i].getQuality() < 20) {
    	                items[i].setQuality(items[i].getQuality() + 1);
    	            }
    	        }
    	    }
    	}
    }
};

int main() {
    Item normalItem("Normal Item", 4, 6);
    Item cheese("Cheese", 4, 10);
    Item concertTicket("Concert Ticket", 4, 14);
    Item excalibur("Excalibur", 4, 10);
    vector<Item> itemsInStock = {normalItem, cheese, concertTicket, excalibur};
    Shop qualityShop(itemsInStock);
    int workingDays = 6;
    
    for (int i = 0; i < workingDays; i++) {
        cout << "Day " << i+1 << endl;
    	qualityShop.updateItems();
    	for (int j = 0; j < qualityShop.getItems().size(); j++) {
            cout << qualityShop.getItems()[j].getName() << ": Quality(" << qualityShop.getItems()[j].getQuality() << "), expires in " << qualityShop.getItems()[j].getExpiresIn() << " days." << endl;
    	}
    }

    return 0;
}
```

Il a décidé de virer le gobelin en charge et de vous employer pour améliorer et maintenir son système. Il veut toujours garder la même organisation du magasin (les classes `Item` et `Shop` doivent avoir les mêmes attributs et constructeurs). 

Vous devez comprendre comment les différents items se comportent et lui expliquer leur fonctionnement. Puis, vous devez refactoriser la fonction `updateItems` (et rajouter d'autres méthodes selon vos besoins) selon les principes du code propre; sinon, vous risquez de partager le même sort que le gobelin !

Compiler et exécuter le code. Observer les variations de qualité des items et répondre aux questions suivantes.

**Question 5** : Quelle est la valeur minimale de la qualité d'un item ?

**Question 6** : Quelle est la valeur maximale de la qualité d'un item ?

**Question 7** : Avant la date d'expiration, la qualité de `Normal Item` varie de combien par jour ? (-x si elle diminue, x si elle augmente)

**Question 8** : Avant la date d'expiration, la qualité de `Cheese` varie de combien par jour ?

**Question 9** : Avant la date d'expiration, la qualité de `Concert Ticket` varie de combien par jour ?

**Question 10** : Avant la date d'expiration, la qualité de `Excalibur` varie de combien par jour ?

**Question 11** : Après la date d'expiration, la qualité de `Normal Item` varie de combien par jour ?

**Question 12** : Après la date d'expiration, la qualité de `Cheese` varie de combien par jour ?

**Question 13** : Après la date d'expiration, quelle est la qualité de `Concert Ticket` ?

**Question 14** : Quelle est la spécialité de `Excalibur` ?

Maintenant que vous avez compris le comportement des différents items, il faut réécrire `updateItems` avec des (sous-)méthodes auxiliaires en respectant les principes vus en cours.
- La fonction `updateItems` devrait faire seulement parcourir la liste des items et de faire `updateItem` pour chaque item. Il ne faut pas oublier de remettre la qualité au niveaux autorisés (en appelant une méthode `resetQualityToWithinRange` après l'update par exemple).
- Vu qu'il y a plusieurs types d'item différents, `updateItem` devrait identifier l'item concerné et appeler la méthode correspondante (par exemple `updateNormalItem`, `updateCheese`, ...). 
- Chaque méthode `update<nom de l'item>` devrait changer les atrributs de l'item selon le comportement que vous avez observé.
- Dans `resetQualityToWithinRange`, il faut remettre la qualité au niveau minimum (ou maximum) si nous avons descendu en dessous (ou dépasser) des niveaux possibles (rappeler vous qu'il faut éviter les [*magic numbers*](#tp2-magic-numbers)).

:::{warning} Ne pas travailler sur une copie de l'objet !
:class: dropdown
Si vous faites `updateItem(Item item)`, alors vous allez travailler sur une copie de l'item appelé en argument. Il ne faut pas oublier de rajouter `&` (par exemple `updateItem(Item& item)`).
:::

**Question 15** : Combien de méthodes (au moins) doit-il y avoir dans `Shop` (sans compter les getters et setters) ?

Comparer le code que vous avez obtenu au code initial. Si vous avez bien fait votre travail, vous devez voir tout de suite la différence de **qualité** entre les deux. 

Maintenant que le nouveau propriétaire a compris comment fonctionnent les items de son magasin, il pense qu'il est intéressant de vendre un item spécial dont la qualité diminue deux fois plus vite qu'un item normal mais après sa date d'expiration, sa qualité augmente de 4 par jour !

`Special Item` commence avec une date d'expiration de 3 jours et une qualité de 4. Implémenter `"Special Item"` dans votre code et rajouter-le dans `main` avec les autres items.

**Question 16** : Quelle est la qualité de l'item spécial lors du sixième jour ?

:::{note} Facile à maintenir et modulable !
:class: dropdown
Normalement, le code est clair même sans commentaire. De plus, il devrait être facile de rajouter l'item spécial. C'est un signe que votre code est facile à maintenir et modulable !
:::