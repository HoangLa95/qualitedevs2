# Comment tester le code ?

```{figure} ../images/unit-testing.jpg
:alt: Tests unitaires
:align: center

Je trouve votre manque de tests unitaires pertubant (Credits: [Emanuil Slavov](https://emanuilslavov.com/on-unit-tests/)).
```

## Tests unitaires

Nous allons utiliser le framework **Google Test**.

```{code} cpp
int factorial(int number);

TEST(factorialTest, handlesZeroInput) {
    EXPECT_EQ(factorial(0), 1);
}

TEST(factorialTest, handlesSomePositiveInput) {
    EXPECT_EQ(factorial(1), 1);
    EXPECT_EQ(factorial(2), 2);
    EXPECT_EQ(factorial(3), 6);
    EXPECT_EQ(factorial(8), 40320);
}
```

Convention générale de nommage :

```{code} cpp
functionType function();

TEST(functionTest, firstTestName) {
    // First Test
}

TEST(functionTest, secondTestName) {
    // Second test
}
```

Plus de détails dans le TP6 et [la documentation de Google Test](https://google.github.io/googletest/).

## Test Driven Development (TDD)

:::{important} Développment piloté par les Tests
:class: dropdown
Le **TDD** est une méthode de développement qui consiste en des cycles de développement très courts. Le principe de cette méthode est d'écrire un test qui impose un comportement au code puis produire le code correspondant et est écrit d'abord puis le code correspondant, puis un autre test et ainsi de suite. 
:::

:::{important} Les trois lois du TDD :
- Écrivez un test qui échoue avant d’écrire le code correspondant.
- Écrivez un seul test à la fois.
- Écrivez le minimum de code qui satisfait le test courant.
:::

```{figure} ../images/test-driven-development.png
:alt: Test Driven Development
:align: center

Le workflow du TDD (Credits: Medium.com).
```

:::{note} Le cycle de développement
:class: dropdown
1. Écrire un seul test.
2. Vérifier que le test échoue (le code correspondant n'existe pas).
3. Écrire juste assez de code pour que le test réussisse.
4. Vérifier que ce test et tous les tests précédents passent.
5. Refactoriser le code tout en gardant les mêmes fonctionnalités (en testant constamment).
:::

## Principes du test propre

:::{important} Un seul concept par test
:class: dropdown
Un test doit tester un seul concept. Souvent, un test consiste en une seule assertion à tester.
:::

Les cinq règles du code propre **F.I.R.S.T.**:
- **Fast**
- **Independent**
- **Repeatable**
- **Self-Validating**
- **Timely**

```{code} cpp
// Don't run unless you have some time to kill.
TEST(parseFileTest, testWithReallyBigFile)
```

:::{danger} not Fast
:class: dropdown
Un test rapide est un test qui peut être exécuter rapidement. Si un test est lent, alors ce test ne sera pas souvent exécuté. Si un test n'est pas exécuté, alors ce n'est pas un test.
:::

```{code} cpp
int GLOBAL_VARIABLE = 10;

int identityFunction(int value);

TEST(identityFunctionTest, incrementValue) {
    GLOBAL_VARIABLE++;
    EXPECT_EQ(identityFunction(GLOBAL_VARIABLE),11);
}

TEST(identityFunctionTest, checkValue) {
    EXPECT_EQ(identityFunction(GLOBAL_VARIABLE), 10);
}
```

:::{danger} not Independent
:class: dropdown
Un test ne doit pas dépendre d'un autre test. Les tests doivent pouvoir être exécutés dans n'importe quel ordre. Si un test qui ne passe pas entraîne d'autres tests à échouer alors on ne pourra pas cerner le problème du code. 
:::

```{code} cpp
#include <chrono>

SomeType someFunction(chrono::weekday day);

TEST(someFunctionTest, isTrueOnMondayOnly) {
    auto currentTime = chrono::system_clock::now();
    auto currentDay = chrono::weekday(currentTime);
    if (currentDay == chrono::Monday)
        EXPECT_TRUE(someFunction(currentDay));
    else
        EXPECT_FALSE(someFunction(currentDay));
}
```

:::{danger} not Repeatable
:class: dropdown
Un test doit pouvoir être répété dans n'importe quel environnement et il testera toujours le même concept. Si le comportement du test change (parfois il passe, parfois il échoue), alors nous ne pouvons pas cerner le problème du code.
:::

```{code} cpp
void produceComplexLog(ArgumentType argument);

TEST(produceComplexLogTest, checkLogOnSomeArgument) {
    ArgumentType someArgument;
    // Code defining someArgument
    // ...

    produceComplexLog(someArgument);
}
```

:::{danger} not Self-Validating
:class: dropdown
Un test est booléen. Soit il passe, soit il échoue. Un test qui passe "à moitié" n'est pas un test. Cela implique que le test doit contenir des assertions (booléennes).
:::

```{figure} ../images/timely.jpg
:alt: Timely TDD meme
:align: center

Credits: imgflip.com/memegenerator
```

:::{danger} not Timely
:class: dropdown
On fait souvent l'erreur de commencer par coder avant d'écrire les tests unitaires. Puis, on se concentre seulement sur l'écriture des tests qui vont passer avec le code que l'on a déjà écrit et on a du mal à trouver d'autres tests. Ce manque de recul vient du fait que l'on a fait des choix d'implémentations qui restreint notre vision des choses. Si on arrive à trouver un test qui ne passe pas, souvent on doit repenser une grande partie du programme. **Écrire le test avant de coder** permet de bien choisir une implémentation qui répondrait à ce qui attendu.  
:::