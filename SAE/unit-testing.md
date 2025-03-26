# Tests unitaires en Java

:::{important} Quiz Git
N'oubliez pas d'utiliser les branches Git et de faire le Quiz sur le Moodle de la SAÉ, dont la date limite est le dimanche 30/03/2025 à 23h59.
:::

:::{note} Disclaimer
:class: dropdown
Nous allons écrire des tests unitaires manuellement (sans framework).
Vous allez apprendre à écrire des tests unitaires avec le framework JUnit (Java Unit Test) en S4.
:::

## Structure du projet

Supposons que votre projet a la structure suivante :
```{code}
src/
    myfirstpackage/
        MyFirstClass.java
        MySecondClass.java
    mysecondpackage/
        MyThirdClass.java
        MyFourthClass.java
```

Dans ce cas, vous allez créer un répertoire `tests/` qui aura la même structure et sera au même niveau que le répertoire `src/` :
```{code}
src/
    myfirstpackage/
        MyFirstClass.java
        MySecondClass.java
    mysecondpackage/
        MyThirdClass.java
        MyFourthClass.java
tests/
    myfirstpackagetest/
        MyFirstClassTest.java
        MySecondClassTest.java
    mysecondpackagetest/
        MyThirdClassTest.java
        MyFourthClassTest.java
```

## Compilation automatique des tests

Pour compiler automatiquement les tests unitaires sous Eclipse :
1. Dans **Project**, assurez-vous que l'option **Build automatically** est activée.
2. Dans **File** > **Properties**, cliquez sur **Java Build Path**.
3. Dans l'onglet **Source**, cliquez sur le bouton **Add Folder** et ajoutez le répertoire `tests/`.
4. Nous avons besoin des assertions pour les tests unitaires mais `assert` est ignoré par défaut en Java, il faut donc ajouter l'option `-ea` (*enable assertions*) dans **Run** > **Run configurations...** > **Arguments** > **VM arguments**.

## Syntaxes

Voici un example simple de test unitaire en Java :
::::{tab-set}
:::{tab-item} MyFirstClass.java
```{code} java
package myfirstpackage;

public class MyFirstClass {
    public static int myStaticMethod(int myArgument) {
        // Some code
        throw new IllegalArgumentException("Some exception");
        // Some more code
        return 0; // Some return value
    }
}
```
:::
:::{tab-item} MyFirstClassTest.java
```{code} java
package myfirstpackagetest;
import myfirstpackage.MyFirstClass;

public class MyFirstClassTest {
    public static void main(String[] args) {
        myStaticMethod_NormalInput_ExpectedReturnValue();
        myStaticMethod_IllegalInput_ThrowsIllegalArgumentException();
        System.out.println("All tests passed.");
    }

    private static void myStaticMethod_NormalInput_ExpectedReturnValue() {
        assert(MyFirstClass.myStaticMethod(0) == 0);
        System.out.println("myStaticMethod_NormalInput_ExpectedReturnValue passed.");
    }

    private static void myStaticMethod_IllegalInput_ThrowsIllegalArgumentException() {
        try {
            MyFirstClass.myStaticMethod(-1);
            assert false : "No exception thrown.";
        } catch (IllegalArgumentException error) {
            System.out.println("myStaticMethod_IllegalInput_ThrowsIllegalArgumentException passed.");
        } catch (Exception error) {
            assert false : "Expected IllegalArgumentException.";
        }
    }
}
```
:::
::::

:::{important} `assert(condition) : "error message"`
En Java, il est possible d'écrire des assertions avec `assert(condition)` ou avec `assert(condition) : "message d'erreur"`, ce qui affichera un message si la condition n'est pas satisfaite. Ce message peut vous aider à identifier les erreurs dans votre code ou vos tests.
:::

Voici un example ou nous pouvons créer des objets avec des attributs et les tester :
::::{tab-set}
:::{tab-item} MySecondClass.java
```{code} java
package myfirstpackage;

public class MySecondClass {
    private int myPrivateAttribute;

    public MySecondClass(int someValue) {
        this.myPrivateAttribute = someValue;
    }

    public int getMyPrivateAttribute() {
        return this.myPrivateAttribute;
    }

    public int myNonStaticMethod(int myArgument) {
        return this.myPrivateAttribute + myArgument; // Example return value
    }
}
```
:::
:::{tab-item} MySecondClassTest.java
```{code} java
package myfirstpackagetest;
import myfirstpackage.MySecondClass;

public class MySecondClassTest {
    private MySecondClass mySecondClassObject;

    public static void main(String[] args) {
        MySecondClassTest test = new MySecondClassTest();
        test.getMyPrivateAttribute_NormalInput_ExpectedReturnValue();
        test.myNonStaticMethod_NormalInput_ExpectedReturnValue();
        System.out.println("All tests passed.");
    }

    private void setup(int someValue) {
        mySecondClassObject = new MySecondClass(someValue);
    }

    private void getMyPrivateAttribute_NormalInput_ExpectedReturnValue() {
        setup(1);
        assert(mySecondClassObject.getMyPrivateAttribute() == 1);
        System.out.println("getMyPrivateAttribute_NormalInput_ExpectedReturnValue passed.");
    }

    private void myNonStaticMethod_NormalInput_ExpectedReturnValue() {
        setup(2);
        assert(mySecondClassObject.myNonStaticMethod(3) == 5);
        System.out.println("myNonStaticMethod_NormalInput_ExpectedReturnValue passed.");
    }
}
```
:::
::::

:::{important} Classe de test avec un attribut
Vous pouvez créer une classe de test qui contient un attribut qui est l'objet à tester.
La méthode `setup` permet de réinitialiser l'objet à tester. Il est important de l'appeler avant chaque test pour garantir que vos tests sont indépendant.
Les tests ne sont donc plus `static` car ils modifient la classe de test à cause de `setup`.
Cette façon de définir les tests permet de refactoriser les tests avec `setup`.
:::

## Instructions pour le projet

- Il est important de se répartir les fonctionnalités à tester entre les membres du groupe.
- Vous n'avez pas besoin de tester l'affichage et les constructeurs qui ne contiennent pas de logique (en plus d'affecter des valeurs aux attributs), pareil pour les accesseurs qui ne contiennent pas de logique supplémentaire.
- Idéalement, le membre qui implémente les tests pour une fonctionnalité ne doit pas être le même que celui qui implémente la fonctionnalité.
- Seulement les méthodes publiques doivent être testées.
- Remplissez votre fichier README pour expliquer comment ouvrir et lancer le projet Eclipse et les tests une fois que nous avons cloné votre dépôt.
- Indiquez qui a écrit des tests pour quelle classe et quelles méthodes dans votre README.

:::{important} Deadline
Seuls les commits avant le dimanche 13/04/2025 à 23h59 sont considérés.
:::