# Comment tester le code ?

:::{warning} Deadline pour rendre votre dépôt distant
Dernier push avant le jeudi 27/03/2025 à 16h !
:::

:::{warning} Plagiat
Il faut me rajouter (Hoang La, *hla* sur GitLab) en tant que Maintainer pour que je puisse vérifier le plagiat de code dans la promo.
Vous seriez convoqués s'il y a des suspicions de plagiat.
:::

:::{warning} Révision
Révisez pour le quiz final, vous n'auriez pas accès aux anciens Quiz pendant la dernière séance.
:::

## Tests unitaires

Vous apprendrez à utiliser des frameworks de test (comme JUnit en Java) en S4.  
Pour l'instant, nous écrirons des tests manuellement.

```{code} cpp
:filename: calculator.h
#ifndef CALCULATOR_H
#define CALCULATOR_H

class Calculator {
public:
    static int factorial(int number);
    static double divide(double numerator, double denominator); 
};

#endif
```

```{code} cpp
:filename: calculator-test.h
#ifndef CALCULATOR_TEST_H
#define CALCULATOR_TEST_H

class CalculatorTest {
public :
    static void runTests();

private :
    static void factorial_ZeroInput_ReturnsOne();
    static void factorial_PositiveInput_ReturnsFactorial();
    static void factorial_NegativeInput_ThrowsInvalidArgument();
    static void divide_NonZeroDenominator_ReturnsDivision();
    static void divide_ZeroDenominator_ThrowsInvalidArgument();
};

#endif
```

Convention générale de nommage : 
```{code} cpp
:filename: object-test.h
#ifndef OBJECT_TEST_H
#define OBJECT_TEST_H

// Same name as the class, with Test added at the end
class ObjectTest {
public :
    static void runTests(); // Method which calls all private tests
private :
    // Tests should be:
    // - static (does not depend on an instance),
    // - void (have no return value),
    // - and take no arguments.
    static void firstMethod_StateUnderTest_ExpectedBehavior();
    static void secondMethod_StateUnderTest_ExpectedBehavior();

    // There are no attributes
};

#endif
```

:::{seealso} Tests paramétrés  
:class: dropdown  
Il existe également des tests paramétrés qui peuvent dépendre des instances d'objets, auquel cas la classe de test peut avoir des attributs, d'autres types de méthodes, et les tests ne seront plus statiques.
:::

Exemple de tests :
```{code} cpp
:filename: calculator-test.cpp
#include "calculator.h"
#include "calculator-test.h"
#include <iostream>
#include <cassert>
#include <stdexcept>

void CalculatorTest::runTests() {
    factorial_ZeroInput_ReturnsOne();
    factorial_PositiveInput_ReturnsFactorial();
    factorial_NegativeInput_ThrowsInvalidArgument();
    divide_NonZeroDenominator_ReturnsDivision();
    divide_ZeroDenominator_ThrowsInvalidArgument();
    std::cout << "All tests passed\n";
}

void CalculatorTest::factorial_ZeroInput_ReturnsOne() {
    assert(Calculator::factorial(0) == 1);
    std::cout << "factorial_ZeroInput_ReturnsOne passed\n";
}

void CalculatorTest::factorial_PositiveInput_ReturnsFactorial() {
    assert(Calculator::factorial(1) == 1);
    assert(Calculator::factorial(2) == 2);
    assert(Calculator::factorial(3) == 6);
    assert(Calculator::factorial(4) == 24);
    assert(Calculator::factorial(5) == 120);
    assert(Calculator::factorial(10) == 3628800);
    std::cout << "factorial_PositiveInput_ReturnsFactorial passed\n";
}

void CalculatorTest::factorial_NegativeInput_ThrowsInvalidArgument() {
    try {
        Calculator::factorial(-1);
        assert(false); // Should not reach this line
    } catch (const std::invalid_argument& error) {
        std::cout << "factorial_NegativeInput_ThrowsInvalidArgument passed\n";
    } catch (...) {
        assert(false); // Catch unexpected exceptions
    }
}

void CalculatorTest::divide_NonZeroDenominator_ReturnsDivision() {
    assert(Calculator::divide(100, 5) == 20.0);
    assert(Calculator::divide(45, 9) == 5.0);
    assert(Calculator::divide(-25, 5) == -5.0);
    assert(Calculator::divide(7, -2) == -3.5);
    assert(Calculator::divide(-36, -6) == 6.0);
    assert(Calculator::divide(0, 3) == 0.0);
    std::cout << "divide_NonZeroDenominator_ReturnsDivision passed\n";
}

void CalculatorTest::divide_ZeroDenominator_ThrowsInvalidArgument() {
    try {
        Calculator::divide(10, 0);
        assert(false); // Should not reach this line
    } catch (const std::invalid_argument& error) {
        std::cout << "divide_ZeroDenominator_ThrowsInvalidArgument passed\n";
    } catch (...) {
        assert(false); // Catch unexpected exceptions
    }
}
```

```{code} cpp
:filename: main.cpp
#include "calculator-test.h"

int main() {
    CalculatorTest::runTests();
    return 0;
}
```
:::{note} Organisation des tests et compilation  
:class: dropdown  
Nous pouvons organiser nos tests dans un dossier `tests/` au même niveau que `source/`, et créer une cible `test` dans notre makefile pour compiler et exécuter les tests.  
:::

:::{seealso} Framework de test et intégration continue  
:class: dropdown  
Les frameworks de test permettent d'automatiser le processus de test. Il n'est plus nécessaire d'afficher les messages manuellement, d'utiliser `runTests` ou de créer un `main` pour exécuter les tests nous-mêmes. De plus, ces frameworks offrent d'autres fonctionnalités, dont certaines que nous allons simuler en TP.

L'intégration continue permet également de tester le code lorsqu'une merge request (demande de fusion d'une branche de développement avec la branche principale) est effectuée, afin de s'assurer qu'un développeur n'introduit pas d'erreurs dans la branche principale. L'écriture et l'utilisation des tests unitaires sont donc essentielles pour le développement d'applications.

Ces notions seront abordées plus en détail en S4.  
:::

## Test Driven Development (TDD)

:::{important} Développement piloté par les tests  
:class: dropdown  
Le **TDD** (Test-Driven Development) est une méthode de développement qui consiste en des itérations successives très courtes. Chaque itération consiste à écrire un test avant d'écrire le code source correspondant, et le code est continuellement refactorisé.  
:::

```{figure} ../images/test-driven-development.png
:alt: Test Driven Development
:align: center

Le cycle de développement du TDD (Credits: Medium.com).
```

:::{important} Le cycle de développement  
:class: dropdown  
Le TDD impose un cycle très court avec trois étapes :  
1. Écrire un seul test.  
2. Passer le test avec le minimum de code nécessaire.  
3. Refactoriser et répéter l'étape 1.

Nous allons suivre l'idée du TDD sans l'appliquer à la lettre. Nos cycles de développement ressembleront plutôt au suivant :  
1. Écrire tous les tests pour la fonction que nous allons implémenter.  
2. Passer les tests progressivement au lieu d'essayer de les passer tous en même temps.  
3. Refactoriser le code au fur et à mesure que nous passons les tests.  
4. Ajouter des tests s'il en manque et revenir à l'étape 2.

Il est important de ne pas essayer de passer trop de tests en même temps, car cela nous permet de plus facilement repérer l'origine des erreurs dans le code. Ce cycle de développement peut aussi être effectué en binôme, avec un membre qui écrit les tests et l'autre qui implémente la fonction.  
:::

Un [exemple](https://quirkylock.netlify.app/) qui illustre bien ce cycle de développement !

## Principes du test propre

:::{important} FIRST
Les cinq règles du test propre :
- **Fast**
- **Independent**
- **Repeatable**
- **Self-Validating**
- **Targeted**
:::

```{code} cpp
void StringProcessorTest::reverse_MultipleScenarios_ReturnsVariousResults() {
    assert(StringProcessor::reverse("hello") == "olleh"); // Normal case
    assert(StringProcessor::reverse("") == "");  // Empty string
    assert(StringProcessor::reverse("a") == "a");  // Single character
    assert(StringProcessor::reverse("racecar") == "racecar");  // Palindrome
    assert(StringProcessor::reverse("12345") == "54321");  // Numeric string
    std::cout << "reverse_MultipleScenarios_ReturnsVariousResults passed\n";
}
```
:::{error}  
:class: dropdown  
**Not Targeted** : Comme le principe de la responsabilité unique pour l'implémentation des fonctions, chaque test doit tester un seul concept. S'il échoue, nous savons d'où vient l'origine de l'erreur dans le code.  
:::

```{code} cpp
void LogTest::produceComplexLog_SomeArgument_ReturnsSomeComplexLog() {
    ArgumentType someArgument;
    // Code defining someArgument
    //...
    Log::produceComplexLog(someArgument);
}
```

:::{error}
:class: dropdown
**not Self-Validating** : Un test est booléen. Soit il passe, soit il échoue. Un test qui passe "à moitié" n'est pas un test. Cela implique que le test doit contenir des assertions.
:::

```{code} cpp
void SomeClassTest::someFunction_SomeArgument_ReturnsTrueOnMondayOnly () {
    auto currentTime = chrono::system_clock::now();
    auto currentDay = chrono::weekday(currentTime);
    if (currentDay == chrono::Monday)
        assert(SomeClass::someFunction(currentDay));
    else
        assert(!SomeClass::someFunction(currentDay));
    std::cout << "someFunction_SomeArgument_ReturnsTrueOnMondayOnly passed\n";
}
```

:::{error} 
:class: dropdown
**Not Repeatable** : Un test doit pouvoir être répété dans n'importe quel environnement et il testera toujours le même concept. Si le comportement du test change (parfois il passe, parfois il échoue), alors nous ne pouvons pas cerner le problème du code.
:::

```{code} cpp
// In the header
class SomeClassTest{
private :
    int someValue = 10;
//...
}

// In the test code

void SomeClassTest::getSomeValue_IncrementValue_Returns11() {
    someValue++;
    assert(SomeClass::getSomeValue() == 11);
    std::cout << "getSomeValue_IncrementValue_Returns11 passed\n";
}

void SomeClassTest::getSomeValue_OriginalValue_Returns10() {
    assert(SomeClass::getSomeValue() == 10);
    std::cout << "getSomeValue_OriginalValue_Returns10 passed\n";
}
```

:::{error} 
:class: dropdown
**Not Independent** : Un test ne doit pas dépendre d'un autre test. Les tests doivent pouvoir être exécutés dans n'importe quel ordre. Si un test qui ne passe pas entraîne d'autres tests à échouer, alors on ne pourra pas cerner le problème du code. 
:::

```{code} cpp
// Don't run unless you have some time to kill
void ParserTest::parseFile_ReallyBigFile_ReturnsCorrectString() {
    //...
}
```

:::{error} 
:class: dropdown
**Not Fast** : Un test rapide est un test qui peut être exécuté rapidement. Si un test est lent, alors ce test ne sera pas souvent exécuté. Si un test n'est pas exécuté, alors ce n'est pas un test.
:::
