# PS6 : Unit tests

(tp6-objectives)=
The objectives of this session is to understand the following points:
- [ ] [The project structure and unit tests](#exercice-1--fizzbuzz)
- [ ] [Unit test syntax](../Cours/cm6.md/#unit-tests)
- [ ] [The TDD development cycle](../Cours/cm6.md/#test-driven-development-tdd)

:::{seealso} WSL
:class: dropdown
If you need to finish the exercises at home and only have access to a Windows environment, you can use the WSL (Windows Subsystem for Linux) with Ubuntu (detailed guide [here](../TechSupport/wsl.md)).
:::

## GitLab Repository Reminder

:::{attention} Add me to your GitLab repository
:class: dropdown
Reminder to add me (Hoang La *hla*) as a **Maintainer** on your GitLab repository.
:::

:::{warning} If you still have issues due to account merging...
:class: dropdown
Please contact the CCRI, then your supervisor, and then me (if the problem persists).
:::

:::{warning} Verify your repository address
:class: dropdown
You should typically name your repository `qualite-dev-s2-<first name>-<last name>`.
If you made a mistake and changed your project's name after creation, note that only the name on the GitLab interface will be updated.
In other words, the address to clone your project will still use the old (incorrect) name.

You can change this address by clicking on **Settings** > **General** > **Advanced**, then changing the address in **Change path**.
Remember to update the synchronization address of your local repository with: `git remote set-url origin <new address with login and PAT>`.
:::

## Exercise 1: FizzBuzz

1. In `PS6/`, create the directory `fizz-buzz/` with the following structure:

```{code}
fizz-buzz/
    source/
        fizz-buzz.cpp
        fizz-buzz.h
        main.cpp
        makefile
    tests/
        fizz-buzz-test.cpp
        fizz-buzz-test.h
        main.cpp
        makefile
    makefile
```

2. Copy the following code into the makefile at the root of `fizz-buzz/`.

```{code} makefile
:filename: fizz-buzz/makefile
all: build source

build:
	mkdir -p build/dependencies/ build/objects build/binaries

source:
	make -C source

run:
	make -C source run

test: source
	make -C tests
	make -C tests run

clean:
	rm -rf build

.PHONY: all build source run test clean
```

3. Copy the following code into the files in `source/`.

::::{tab-set}
:::{tab-item} fizz-buzz.cpp
```{code} cpp
#include "fizz-buzz.h"
#include <string>
#include <stdexcept>

std::string FizzBuzz::fizzBuzz(int number) {
	std::string result = "";
	return result;
}
```
:::
:::{tab-item} fizz-buzz.h
```{code} cpp
#ifndef FIZZ_BUZZ_H
#define FIZZ_BUZZ_H

#include <string>

/**
 * Contains only the fizzBuzz method.
 * 
 * fizzBuzz is a method that takes a positive integer as input
 * and outputs a string according to the some rules related to
 * the divisibility by 2 or 7, and the presence of the digits 2 or 7.
 */
class FizzBuzz {
public:
    /**
    * Returns a string depending on the number.
    *
    * fizzBuzz returns the string according to the following rules:
    * 1. if number is even, then the string contains a "Fizz";
    * 2. if number is divisible by 7, then the string contains a "Buzz";
    * 3. if number contains 2, then the string contains a "Fizz";
    * 4. if number contains 7, then the string contains a "Buzz";
    * 5. each "Fizz" or "Buzz" correspond to exactly one rule from 1. to 4.;
    * 6. if none of 1. to 4. is true, then the string is the number itself;
    * 7. the string must not contain more characters than necessary.
    *
    * @param number A positive integer.
    * @return The string according to the rules above.
    * @throw std::invalid_argument when number is not positive.
    */
    static std::string fizzBuzz(int number); 
};

/**
 * @example
 * \code{.cpp}
 * std::cout << FizzBuzz::fizzBuzz(1); \\ Output: "1"
 * std::cout << FizzBuzz::fizzBuzz(2); \\ Output: "FizzFizz"
 * std::cout << FizzBuzz::fizzBuzz(7); \\ Output: "BuzzBuzz"
 * std::cout << FizzBuzz::fizzBuzz(728); \\ Output: "FizzFizzBuzzBuzz"
 * \endcode
 */

#endif
```
:::
:::{tab-item} main.cpp
```{code} cpp
#include "fizz-buzz.h"
#include <iostream>

int main() {
    int number;
    std::cout << "Enter a number: ";
    std::cin >> number;
    std::cout << FizzBuzz::fizzBuzz(number) << std::endl;
    return 0;
}
```
:::
:::{tab-item} makefile
```{code} makefile
all: build ../build/binaries/source/fizz-buzz

build:
	mkdir -p ../build/dependencies/source ../build/objects/source ../build/binaries/source

../build/binaries/source/fizz-buzz: ../build/objects/source/main.o ../build/objects/source/fizz-buzz.o
	g++ -o ../build/binaries/source/fizz-buzz ../build/objects/source/main.o ../build/objects/source/fizz-buzz.o

../build/objects/source/main.o: main.cpp
	g++ -c main.cpp -o ../build/objects/source/main.o -MMD -MF ../build/dependencies/source/main.d

../build/objects/source/fizz-buzz.o: fizz-buzz.cpp
	g++ -c fizz-buzz.cpp -o ../build/objects/source/fizz-buzz.o -MMD -MF ../build/dependencies/source/fizz-buzz.d  

run:
	../build/binaries/source/fizz-buzz

clean:
	rm -rf ../build/dependencies/source ../build/objects/source ../build/binaries/source

.PHONY: all clean build

-include ../build/dependencies/source/*.d
```
:::
::::

4. Recopiez les codes suivants pour les fichiers de `test/`.

::::{tab-set}
:::{tab-item} fizz-buzz-test.cpp
```{code} cpp
#include "../source/fizz-buzz.h"
#include "fizz-buzz-test.h"
#include <iostream>
#include <cassert>
#include <stdexcept>

void FizzBuzzTest::runTests() {
    fizzBuzz_NegativeInput_ReturnsInvalidArgument();
	fizzBuzz_ZeroInput_ReturnsInvalidArgument();
    fizzBuzz_OddInputNonDivisibleBy7DoesNotContain2Or7_ReturnsTheSameNumber();
    fizzBuzz_EvenInputNonDivisibleBy7DoesNotContain2Or7_ReturnsFizz();
    fizzBuzz_EvenInputNonDivisibleBy7Contains2ButNot7_ReturnsFizzFizz();
    fizzBuzz_OddInputDivisibleBy7DoesNotContain2Or7_ReturnsBuzz();
    fizzBuzz_OddInputDivisibleBy7Contains7ButNot2_ReturnsBuzzBuzz();
    fizzBuzz_EvenInputDivisibleBy7DoesNotContain2Or7_ReturnsFizzBuzz();
    fizzBuzz_EvenInputNonDivisibleBy7Contains7ButNot2_ReturnsFizzBuzz();
    fizzBuzz_OddInputDivisibleBy7Contains2ButNot7_ReturnsFizzBuzz();
    fizzBuzz_OddInputNonDivisibleBy7Contains2And7_ReturnsFizzBuzz();
    fizzBuzz_OddInputDivisibleBy7Contains2And7_ReturnsFizzBuzzBuzz();
    fizzBuzz_EvenInputNonDivisibleBy7Contains2And7_ReturnsFizzFizzBuzz();
    fizzBuzz_EvenInputDivisibleBy7Contains2ButNot7_ReturnsFizzFizzBuzz();
    fizzBuzz_EvenInputDivisibleBy7Contains7ButNot2_ReturnsFizzBuzzBuzz();
    fizzBuzz_EvenInputDivisibleBy7Contains2And7_ReturnsFizzFizzBuzzBuzz();
    std::cout << "All tests passed\n";
}

void FizzBuzzTest::fizzBuzz_NegativeInput_ReturnsInvalidArgument() {
	try{
		FizzBuzz::fizzBuzz(-728);
		assert(false);
	} catch (const std::invalid_argument& error) {
		std::cout << "fizzBuzz_NegativeInput_ReturnsInvalidArgument passed\n";
	} catch (...){
		assert(false);
	}
}

void FizzBuzzTest::fizzBuzz_ZeroInput_ReturnsInvalidArgument() {
	try{
		FizzBuzz::fizzBuzz(0);
		assert(false);
	} catch (const std::invalid_argument& error) {
		std::cout << "fizzBuzz_ZeroInput_ReturnsInvalidArgument passed\n";
	} catch (...){
		assert(false);
	}
}

void FizzBuzzTest::fizzBuzz_OddInputNonDivisibleBy7DoesNotContain2Or7_ReturnsTheSameNumber() {
    assert(FizzBuzz::fizzBuzz(1) == "1");
	assert(FizzBuzz::fizzBuzz(3) == "3");
	assert(FizzBuzz::fizzBuzz(5) == "5");
	assert(FizzBuzz::fizzBuzz(11) == "11");
	assert(FizzBuzz::fizzBuzz(13) == "13");
	assert(FizzBuzz::fizzBuzz(19) == "19");
	assert(FizzBuzz::fizzBuzz(31) == "31");
	assert(FizzBuzz::fizzBuzz(41) == "41");
	assert(FizzBuzz::fizzBuzz(43) == "43");
	assert(FizzBuzz::fizzBuzz(53) == "53");
	assert(FizzBuzz::fizzBuzz(59) == "59");
    std::cout << "fizzBuzz_OddInputNonDivisibleBy7DoesNotContain2Or7_ReturnsTheSameNumber passed\n";
}

void FizzBuzzTest::fizzBuzz_EvenInputNonDivisibleBy7DoesNotContain2Or7_ReturnsFizz() {
	assert(FizzBuzz::fizzBuzz(4) == "Fizz");
	assert(FizzBuzz::fizzBuzz(6) == "Fizz");
	assert(FizzBuzz::fizzBuzz(8) == "Fizz");
	assert(FizzBuzz::fizzBuzz(10) == "Fizz");
	assert(FizzBuzz::fizzBuzz(16) == "Fizz");
	assert(FizzBuzz::fizzBuzz(18) == "Fizz");
	assert(FizzBuzz::fizzBuzz(40) == "Fizz");
	assert(FizzBuzz::fizzBuzz(44) == "Fizz");
	assert(FizzBuzz::fizzBuzz(46) == "Fizz");
	assert(FizzBuzz::fizzBuzz(48) == "Fizz");
    std::cout << "fizzBuzz_EvenInputNonDivisibleBy7DoesNotContain2Or7_ReturnsFizz passed\n";
}

void FizzBuzzTest::fizzBuzz_EvenInputNonDivisibleBy7Contains2ButNot7_ReturnsFizzFizz() {
	assert(FizzBuzz::fizzBuzz(2) == "FizzFizz");
	assert(FizzBuzz::fizzBuzz(12) == "FizzFizz");
	assert(FizzBuzz::fizzBuzz(20) == "FizzFizz");
	assert(FizzBuzz::fizzBuzz(24) == "FizzFizz");
	assert(FizzBuzz::fizzBuzz(26) == "FizzFizz");
	assert(FizzBuzz::fizzBuzz(32) == "FizzFizz");
	assert(FizzBuzz::fizzBuzz(62) == "FizzFizz");
	assert(FizzBuzz::fizzBuzz(82) == "FizzFizz");	
    std::cout << "fizzBuzz_EvenInputNonDivisibleBy7Contains2ButNot7_ReturnsFizzFizz passed\n";
}

void FizzBuzzTest::fizzBuzz_OddInputDivisibleBy7DoesNotContain2Or7_ReturnsBuzz() {
    assert(FizzBuzz::fizzBuzz(35) == "Buzz");
	assert(FizzBuzz::fizzBuzz(49) == "Buzz");
	assert(FizzBuzz::fizzBuzz(63) == "Buzz");
	assert(FizzBuzz::fizzBuzz(91) == "Buzz");
	assert(FizzBuzz::fizzBuzz(105) == "Buzz");
	assert(FizzBuzz::fizzBuzz(119) == "Buzz");
    std::cout << "fizzBuzz_OddInputDivisibleBy7DoesNotContain2Or7_ReturnsBuzz passed\n";
}

void FizzBuzzTest::fizzBuzz_OddInputDivisibleBy7Contains7ButNot2_ReturnsBuzzBuzz() {
	assert(FizzBuzz::fizzBuzz(7) == "BuzzBuzz");
	assert(FizzBuzz::fizzBuzz(77) == "BuzzBuzz");
	assert(FizzBuzz::fizzBuzz(147) == "BuzzBuzz");
	assert(FizzBuzz::fizzBuzz(175) == "BuzzBuzz");	
    std::cout << "fizzBuzz_OddInputDivisibleBy7Contains7ButNot2_ReturnsBuzzBuzz passed\n";
}

void FizzBuzzTest::fizzBuzz_EvenInputDivisibleBy7DoesNotContain2Or7_ReturnsFizzBuzz() {
	assert(FizzBuzz::fizzBuzz(14) == "FizzBuzz");
	assert(FizzBuzz::fizzBuzz(56) == "FizzBuzz");
	assert(FizzBuzz::fizzBuzz(84) == "FizzBuzz");
	assert(FizzBuzz::fizzBuzz(98) == "FizzBuzz");
	assert(FizzBuzz::fizzBuzz(140) == "FizzBuzz");
	assert(FizzBuzz::fizzBuzz(154) == "FizzBuzz");	
    std::cout << "fizzBuzz_EvenInputDivisibleBy7DoesNotContain2Or7_ReturnsFizzBuzz passed\n";
}

void FizzBuzzTest::fizzBuzz_EvenInputNonDivisibleBy7Contains7ButNot2_ReturnsFizzBuzz() {
	assert(FizzBuzz::fizzBuzz(74) == "FizzBuzz");
	assert(FizzBuzz::fizzBuzz(76) == "FizzBuzz");
	assert(FizzBuzz::fizzBuzz(78) == "FizzBuzz");
	assert(FizzBuzz::fizzBuzz(170) == "FizzBuzz");
	assert(FizzBuzz::fizzBuzz(174) == "FizzBuzz");
	assert(FizzBuzz::fizzBuzz(176) == "FizzBuzz");
    std::cout << "fizzBuzz_EvenInputNonDivisibleBy7Contains7ButNot2_ReturnsFizzBuzz passed\n";
}

void FizzBuzzTest::fizzBuzz_OddInputDivisibleBy7Contains2ButNot7_ReturnsFizzBuzz() {
	assert(FizzBuzz::fizzBuzz(21) == "FizzBuzz");
	assert(FizzBuzz::fizzBuzz(203) == "FizzBuzz");
	assert(FizzBuzz::fizzBuzz(259) == "FizzBuzz");
	assert(FizzBuzz::fizzBuzz(329) == "FizzBuzz");
    std::cout << "fizzBuzz_OddInputDivisibleBy7Contains2ButNot7_ReturnsFizzBuzz passed\n";
}

void FizzBuzzTest::fizzBuzz_OddInputNonDivisibleBy7Contains2And7_ReturnsFizzBuzz() {
    assert(FizzBuzz::fizzBuzz(27) == "FizzBuzz");
	assert(FizzBuzz::fizzBuzz(127) == "FizzBuzz");
	assert(FizzBuzz::fizzBuzz(527) == "FizzBuzz");
	assert(FizzBuzz::fizzBuzz(927) == "FizzBuzz");
    std::cout << "fizzBuzz_OddInputNonDivisibleBy7Contains2And7_ReturnsFizzBuzz passed\n";
}

void FizzBuzzTest::fizzBuzz_OddInputDivisibleBy7Contains2And7_ReturnsFizzBuzzBuzz() {
	assert(FizzBuzz::fizzBuzz(217) == "FizzBuzzBuzz");
	assert(FizzBuzz::fizzBuzz(273) == "FizzBuzzBuzz");
	assert(FizzBuzz::fizzBuzz(427) == "FizzBuzzBuzz");
	assert(FizzBuzz::fizzBuzz(721) == "FizzBuzzBuzz");
    std::cout << "fizzBuzz_OddInputDivisibleBy7Contains2And7_ReturnsFizzBuzzBuzz passed\n";
}

void FizzBuzzTest::fizzBuzz_EvenInputNonDivisibleBy7Contains2And7_ReturnsFizzFizzBuzz() {
	assert(FizzBuzz::fizzBuzz(72) == "FizzFizzBuzz");
	assert(FizzBuzz::fizzBuzz(272) == "FizzFizzBuzz");
	assert(FizzBuzz::fizzBuzz(274) == "FizzFizzBuzz");
	assert(FizzBuzz::fizzBuzz(726) == "FizzFizzBuzz");
	assert(FizzBuzz::fizzBuzz(872) == "FizzFizzBuzz");
    std::cout << "fizzBuzz_EvenInputNonDivisibleBy7Contains2And7_ReturnsFizzFizzBuzz passed\n";
}

void FizzBuzzTest::fizzBuzz_EvenInputDivisibleBy7Contains2ButNot7_ReturnsFizzFizzBuzz() {
	assert(FizzBuzz::fizzBuzz(28) == "FizzFizzBuzz");
	assert(FizzBuzz::fizzBuzz(42)  == "FizzFizzBuzz");
	assert(FizzBuzz::fizzBuzz(112) == "FizzFizzBuzz");
	assert(FizzBuzz::fizzBuzz(126) == "FizzFizzBuzz");
	assert(FizzBuzz::fizzBuzz(182) == "FizzFizzBuzz");
    std::cout << "fizzBuzz_EvenInputDivisibleBy7Contains2ButNot7_ReturnsFizzFizzBuzz passed\n";
}

void FizzBuzzTest::fizzBuzz_EvenInputDivisibleBy7Contains7ButNot2_ReturnsFizzBuzzBuzz() {
    assert(FizzBuzz::fizzBuzz(70) == "FizzBuzzBuzz");
    assert(FizzBuzz::fizzBuzz(378) == "FizzBuzzBuzz");
    assert(FizzBuzz::fizzBuzz(476) == "FizzBuzzBuzz");
    std::cout << "fizzBuzz_EvenInputDivisibleBy7Contains7ButNot2_ReturnsFizzBuzzBuzz passed\n";
}

void FizzBuzzTest::fizzBuzz_EvenInputDivisibleBy7Contains2And7_ReturnsFizzFizzBuzzBuzz() {
    assert(FizzBuzz::fizzBuzz(728) == "FizzFizzBuzzBuzz");
	assert(FizzBuzz::fizzBuzz(2716) == "FizzFizzBuzzBuzz");
    std::cout << "fizzBuzz_EvenInputDivisibleBy7Contains2And7_ReturnsFizzFizzBuzzBuzz passed\n";
}
```
:::
:::{tab-item} fizz-buzz-test.h
```{code} cpp
#ifndef FIZZ_BUZZ_TEST_H
#define FIZZ_BUZZ_TEST_H

class FizzBuzzTest {
public:
    static void runTests();

private:
    static void fizzBuzz_NegativeInput_ReturnsInvalidArgument();
    static void fizzBuzz_ZeroInput_ReturnsInvalidArgument();
    static void fizzBuzz_OddInputNonDivisibleBy7DoesNotContain2Or7_ReturnsTheSameNumber();
    static void fizzBuzz_EvenInputNonDivisibleBy7DoesNotContain2Or7_ReturnsFizz();
    static void fizzBuzz_EvenInputNonDivisibleBy7Contains2ButNot7_ReturnsFizzFizz();
    static void fizzBuzz_OddInputDivisibleBy7DoesNotContain2Or7_ReturnsBuzz();
    static void fizzBuzz_OddInputDivisibleBy7Contains7ButNot2_ReturnsBuzzBuzz();
    static void fizzBuzz_EvenInputDivisibleBy7DoesNotContain2Or7_ReturnsFizzBuzz();
    static void fizzBuzz_EvenInputNonDivisibleBy7Contains7ButNot2_ReturnsFizzBuzz();
    static void fizzBuzz_OddInputDivisibleBy7Contains2ButNot7_ReturnsFizzBuzz();
    static void fizzBuzz_OddInputNonDivisibleBy7Contains2And7_ReturnsFizzBuzz();
    static void fizzBuzz_OddInputDivisibleBy7Contains2And7_ReturnsFizzBuzzBuzz();
    static void fizzBuzz_EvenInputNonDivisibleBy7Contains2And7_ReturnsFizzFizzBuzz();
    static void fizzBuzz_EvenInputDivisibleBy7Contains2ButNot7_ReturnsFizzFizzBuzz();
    static void fizzBuzz_EvenInputDivisibleBy7Contains7ButNot2_ReturnsFizzBuzzBuzz();
    static void fizzBuzz_EvenInputDivisibleBy7Contains2And7_ReturnsFizzFizzBuzzBuzz();
};

#endif
```
:::
:::{tab-item} main.cpp
```{code} cpp
#include "fizz-buzz-test.h"

int main() {
    FizzBuzzTest::runTests();
    return 0;
}
```
:::
:::{tab-item} makefile
```{code} makefile
all: build ../build/binaries/tests/fizz-buzz-test

build:
	mkdir -p ../build/dependencies/tests ../build/objects/tests ../build/binaries/tests 

../build/binaries/tests/fizz-buzz-test: ../build/objects/tests/main.o ../build/objects/tests/fizz-buzz-test.o
	g++ -o ../build/binaries/tests/fizz-buzz-test ../build/objects/tests/main.o ../build/objects/tests/fizz-buzz-test.o ../build/objects/source/fizz-buzz.o

../build/objects/tests/main.o: main.cpp
	g++ -c main.cpp -o ../build/objects/tests/main.o -MMD -MF ../build/dependencies/tests/main.d  

../build/objects/tests/fizz-buzz-test.o: fizz-buzz-test.cpp
	g++ -c fizz-buzz-test.cpp -o ../build/objects/tests/fizz-buzz-test.o -MMD -MF ../build/dependencies/tests/fizz-buzz-test.d  

run:
	../build/binaries/tests/fizz-buzz-test
	
clean:
	rm -rf ../build/dependencies/tests ../build/objects/tests ../build/binaries/tests

.PHONY: all clean build

-include ../build/dependencies/tests/*.d
```
:::
::::

:::{attention} makefile
:class: dropdown
*Reminder*: `make -C <directory> <target>` is equivalent to `cd <directory>` then `make <target>` inside `<directory>`.

The makefiles here are written in a simple and explicit way (without refactoring).
A clean makefile would use variables to refactor the code.
:::

In this first exercise, the task is to implement the `fizzBuzz` function of the `FizzBuzz` class.
The tests are provided in the `fizz-buzz-test.cpp` file.

The `make` commands provided allow you to compile the code and run the tests.
When you are in the `fizz-buzz/` folder, you can run the following commands:
- `make` to compile the source code.
- `make run` to run the source executable to test the code through terminal inputs.
- `make test` to compile and run the tests.
- you can add the `-s` option to make for a less verbose output (for example `make -s test` or `make -s run`).

5. Read the documentation of the `FizzBuzz` class and the tests.

6. Implement `fizzBuzz` in `fizz-buzz.cpp` so that all tests pass.

:::{hint} Hints
:class: dropdown
- `std::to_string(number)` returns the string representing `number` (for example, `std::to_string(3)` returns `"3"`).
- `a % b` returns `a` modulo `b` (for example, `3 % 2` returns `1`).
- `bigString.find(smallString)!=std::string::npos` returns true if `bigString` contains `smallString`, false otherwise.
- `"3" + "3"` returns `"33"`.
:::

Is your code clean?

## Exercise 2: Sorting Hat

1. In `PS6/`, create the `sorting-hat/` directory.

Here are the codes for the `SortingHat` class and the `main.cpp` to test the class with terminal inputs.

::::{tab-set}
:::{tab-item} main.cpp
```{code} cpp
#include "sorting-hat.h"
#include <iostream>

int main() {
    int mood;
    std::cout << "Is the sorting fair(0) or playful(1)? ";
    std::cin >> mood;
    SortingHat hat(static_cast<Mood>(mood));

    int student;
    std::cout << "What is the student's best quality: ambitious(0), brave(1), intelligent(2), or loyal(3)? ";
    std::cin >> student;

    std::cout << "The perfect fit for you is (Slytherin:0, Gryffindor:1, Ravenclaw:2, Hufflepuff:3): " << hat.assignHouse(static_cast<Student>(student)) << std::endl;
    return 0;
}
```
:::
:::{tab-item} sorting-hat.cpp
```{code} cpp
#include "sorting-hat.h"

SortingHat::SortingHat(Mood mood) : mMood(mood) {}

Mood SortingHat::getMood() const {
    // TODO
	return Mood::Fair;
}

House SortingHat::assignHouse(Student student) const {
	// Bonus
	return House::Slytherin;
}
```
:::
:::{tab-item} sorting-hat.h
```{code} cpp
#ifndef SORTING_HAT_H
#define SORTING_HAT_H

/**
 * The four houses of Hogwarts.
 *
 * The four houses of Hogwarts are: 
 * Slytherin, Gryffindor, Ravenclaw, and Hufflepuff.
 */
enum House {Slytherin, Gryffindor, Ravenclaw, Hufflepuff};

/**
 * The four qualities in a student the Sorting Hat is looking for.
 *
 * The four qualities in a student the Sorting Hat is looking for are:
 * Ambition, Courage, Intelligence, and Loyalty.
 */
enum Student {Ambitious, Brave, Intelligent, Loyal};

/**
 * The two possible mood of the Sorting Hat.
 *
 * The two possible mood of the Sorting Hat are:
 * Fair and Playful.
 */
enum Mood {Fair, Playful};

/**
 * The Sorting Hat which assigns students to houses.
 *
 * The Sorting Hat assign a student to a house based on
 * its mood and the student's best quality.
 */
class SortingHat {
private:
    Mood mMood;

public:
    /**
    * Constructs a SortingHat object.
    *
    * Initializes the SortingHat with a Mood.
    * @param mood The hat's mood.
    */
    SortingHat(const Mood mood);

    /**
    * Gets the sorting hat's mood.
    *
    * @return The hat's mood.
    */
    Mood getMood() const;

    /**
    * Returns the House a Student is assigned to.
    *
    * The hat chooses the House for a student based on its mood
    * and the student's best quality.
    * If the hat is fair, then:
    * - Ambitious students go to Slytherin;
    * - Brave students go to Gryffindor;
    * - Intelligent students go to Ravenclaw;
    * - Loyal students go to Hufflepuff.
    * If the hat is playful, then it never makes the same decision
    * as when it is fair.
    *
    * @param student The student's best quality. 
    * @return The House the student is assigned to.
    */
    House assignHouse(Student student) const;
};

/**
 * @example
 * \code{.cpp}
 * SortingHat fairHat(Mood::Fair);
 * fairHat.assignHouse(Student::Brave); \\ Output: House::Gryffindor
 * SortingHat playfulHat(Mood::Playful);
 * playfulHat.assignHouse(Student::Brave); \\ Outputs a House different from Gryffindor
 * \endcode
 */

#endif
```
:::
::::

We are trying to simulate the behavior of the "Sorting Hat" from the Harry Potter world.
Hogwarts is a school for wizards. It is divided into four houses and the Sorting Hat is a hat that allows wizards to find their house.
This choice is normally based on the four main qualities sought in a wizard, but the Sorting Hat is also influenced by its mood.

The goal of the exercise is to write unit tests to verify the behavior of the Sorting Hat.

2. Create and structure the directories and files of `sorting-hat/` in the same way as you did for `fizz-buzz/`.

:::{hint} makefile
:class: dropdown
Both directories should have the same structure, so you just need to replace `fizz-buzz` with `sorting-hat` in the makefiles.
:::

3. Read the documentation of the `SortingHat` class to understand how it works.

4. Write unit tests to test the two methods `getMood` and `assignHouse` of the `SortingHat` class.

:::{important} Testing the constructor
When the constructor only initializes the class attributes, it is sufficient to test the getters. However, when the constructor contains logic, it is necessary to test the constructor itself.
:::

5. Implement `getMood` in `sorting-hat.cpp`.

6. **Bonus**: Implement `assignHouse` in `sorting-hat.cpp`.

Return to the [objectives](#tp6-objectives) and check off the points you have mastered. Review the points you don't yet understand. Call your instructor if needed.
