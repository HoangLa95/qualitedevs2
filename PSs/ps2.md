# TP2 : How to rename things?

:::{important} Git  
From now on, occasional reminders will be given to help you maintain your repository, but you should be autonomous with Git.  
A quick reminder: here's a typical workflow you'll follow:  
- Run `git remote update` and `git status` to check the state of your repositories.  
- Use `git pull` if your remote repository has more recent changes.  
- At least at the end of each exercise, remember to use `git add`, `git commit`, and `git push` to save your work.  

Don't hesitate to revisit PS1 if you're unsure. The quality of your Git history will be evaluated.  
:::

:::{important} Conflict resolution  
:class: dropdown  
You have not yet learned how to resolve merge conflicts (divergent branches) in Git, which occur when there are multiple versions of the same file in different repositories and you attempt to synchronize them.  
If you encounter difficulties, call your supervisor over.  
Once this skill is mastered, you will be on your own in managing conflicts.  
:::

:::{important} Organization of PSs and exercises
At the beginning of PSX, create a folder named `PSX` in your repository.  
For each exercise titled **"Exercise n: Name of the exercise"**, create a file named `name-of-the-exercise.cpp` (using the **kebab-case** convention) and add the provided code to it.  
:::

(ps2-objectives)=
## Objectives

The goal of this session is to understand the following points:  
- [ ] [Working with Git](ps1.md)  
- [ ] [Magic numbers](#ps2-magic-numbers)  
- [ ] [User input](#user-input)  
- [ ] [Members of a C++ class](#ps2-class-members)  
- [ ] [Getter and setter](#ps2-getter-setter)  
- [ ] [Constructor of a class](#ps2-constructor)  
- [ ] [Different naming conventions](#cm2-camel-case)  
- [ ] [Intention-revealing names](#cm2-nom-revelateur)

## Exercise 1 : Magic numbers

```{code} cpp
#include <iostream>
#include <string>

using namespace std;

bool qualifiesForDiscount(double originalPrice) {
    return originalPrice >= 10;
}

double calculateDiscountPercentage(double originalPrice, bool isStudent, bool isLoyalCustomer) {
    double discountPercentage = 0.0;

    if (qualifiesForDiscount(originalPrice)) {
        if (isStudent) {
            discountPercentage += 10; 
        }
        if (isLoyalCustomer) {
            discountPercentage += 10;
        }
    }

    return discountPercentage;
}

double calculateDiscountAmount(double originalPrice, double discountPercentage) {
    return originalPrice * discountPercentage/100;
}

double calculateFinalPrice(double originalPrice, bool isStudent, bool isLoyalCustomer){
    double discountPercentage = calculateDiscountPercentage(originalPrice, isStudent, isLoyalCustomer);
    double discountAmount = calculateDiscountAmount(originalPrice, discountPercentage);
    double finalPrice = originalPrice - discountAmount;
    return finalPrice;
}

double redeemStudentPoints(double discountAmount){
    return discountAmount*(100-10)/100;
}

double redeemLoyaltyPoints(double discountAmount){
    return discountAmount*(100-2*10)/100;
}

double calculateRewardPoints(double discountAmount, bool isStudent, bool isLoyalCustomer){
    double rewardPoints = 0.0;
    if (isStudent){
        rewardPoints = redeemStudentPoints(discountAmount);
    } else if (isLoyalCustomer){
        rewardPoints = redeemLoyaltyPoints(discountAmount);
    }
    return rewardPoints;
}

int main() {
    const double originalPrice = 100;
    const bool isStudent = true;
    const bool isLoyalCustomer = true;

    cout << "Original price: " << originalPrice << " euros." << endl;

    double finalPrice = calculateFinalPrice(originalPrice,isStudent,isLoyalCustomer);
    cout << "Final price with student and loyalty discounts: " << finalPrice << " euros." << endl;

    double discountAmount = originalPrice - finalPrice;
    cout << "Your reward points: " << calculateRewardPoints(discountAmount,isStudent,isLoyalCustomer) << endl;

    return 0;
}
```

1. Save and compile the code (with `g++ -o magic-numbers magic-numbers.cpp`).

:::{important} Executable Name  
The first `<filename>` in the compilation command corresponds to the name of the executable.  
In this course, we will always use the same name as the `.cpp` file for the executable.  
:::

2. Run `./magic-numbers`.

3. Do you understand what this code does?

:::{hint} Hints  
:class: dropdown  
A customer buys a product at the price of `originalPrice`.  
They can get discounts if they are a student or a loyal customer, and the discounts also allow them to earn reward points.  

The rules are as follows:  

- A discount only applies if `originalPrice` is greater than 10 euros.  
- A student gets a 10% discount.  
- A loyal customer also gets a 10% discount.  
- If the customer is both a student and loyal, the discounts adds up to a total of 20%.  
- With `originalPrice = 100` euros, the total discount is 20 euros.  
- Reward points are calculated by applying a percentage to this discount:  
    - $\frac{100 - \text{student discount}}{100}$ (which gives 90% here) gives 18 points.  
    - $\frac{100 - 2 \times \text{loyal customer discount}}{100}$ (which gives 80% here) gives 16 points.  
- Since the student discount is always more advantageous, it is applied first.  
- Thus, in this example, the customer gets **18 points**.  
:::

4. Although the names of the elements are appropriate, this code is not clean. Can you spot the reasons?

(ps2-magic-numbers)=
:::{hint} Magic numbers
:class: dropdown
Magic numbers refer to numbers that are used directly in the code.  
Often, these numbers appear without a clear justification.  
In this example, although the meaning of some numbers can be inferred from the other well-named elements, it is better to avoid them.

Some magic numbers may be acceptable, such as `100` for percentages, `0` for initializing a variable, or `1` for an increment.  
However, the majority of them should be avoided.

Here, the number `10` appears multiple times, but its meaning varies depending on the context. Imagine that we wanted to modify the eligibility threshold for a discount, adjust the discount percentages, or change the reward formulas. The risk of errors would then become significant. Additionally, it is difficult to identify and modify the correct values without having to dive into the entire code to understand its logic.

The solution is to assign explicit names to these magic numbers based on their scope, whether they are local or global.  
It is essential to follow the proper naming conventions covered in the course to avoid magic numbers.
:::

5. Name the magic numbers.

:::{hint} Have you completed your task satisfactorily?  
:class: dropdown  
A simple way to check if you've applied the naming conventions correctly is to try modifying a single parameter at a time (for example, the student discount percentage, customer discount, etc.).  

- Would you have been able to make this change in a similar 10,000-line code using a search (`Ctrl+f`)?  
- Were you able to modify a single variable (just one line of code)?  

If not, rename them.  
:::

### User input

To avoid recompiling the code every time you modify `originalPrice`, `isStudent`, and `isLoyalCustomer`, we can retrieve the values from the keyboard during execution.

6. Replace the following lines with the code below.

```{code} cpp
const double originalPrice = 100;
const bool isStudent = true;
const bool isLoyalCustomer = true;
```

```{code} cpp
double userOriginalPrice;
cout << "Enter the original price: ";
cin >> userOriginalPrice;
const double& originalPrice = userOriginalPrice;

bool userIsStudent;
cout << "Are you a student? (1:Yes, 0:No) ";
cin >> userIsStudent;
const bool& isStudent = userIsStudent;

bool userIsLoyalCustomer;
cout << "Do you have a loyalty card? (1:Yes, 0:No) ";
cin >> userIsLoyalCustomer;
const bool& isLoyalCustomer = userIsLoyalCustomer;
```

:::{note} `const`
:class: dropdown
Using `const <type>&` for user inputs ensures that these values cannot be accidentally modified in the code, thus preventing unexpected errors. The reference here avoids making copies of variable values, which is especially important when working with objects that consume a lot of memory.

In general, using `const` in front of immutable variables makes the code clearer by explicitly indicating that these values should not be modified, which makes the program easier to maintain and understand.
**Have you correctly used `const` in your code ?**
:::

7. Test your code and answer the questions in the **Quiz**.

:::{important} Git  
:class: dropdown  
Have you remembered to maintain your repositories?  
:::


## Exercise 2 : Classes-and-methods

```{code} cpp
#include <iostream>
using namespace std;

double b1 = 0.0;
double b2 = 100.0;

void d1(double x) {
    b1 += x;
}

void d2(double x) {
    b1 += x;
}

void w1(double x) {
    if (x + 4 <= b1) {
        b1 -= (x + 4);
    } else {
        cout << "Insufficient funds!" << endl;
    }
}

void w2(double x) {
    if (x + 2 <= b2) {
        b2 -= (x + 2);
    } else {
        cout << "Insufficient funds!" << endl;
    }
}

int main() {
    d1(100.0);
    w1(50.0);
    w2(50.0);
    cout << "First account balance: " << b1 << endl;
    cout << "Second account balance: " << b2 << endl;
    return 0;
}
```
1. Do you understand what this code does?

:::{hint} Hints  
:class: dropdown  
- There are two accounts with two different types: type `1`, which we will call "Basic", and type `2`, called "Standard".  
- The first account `b1` has an initial balance of `0.0` euros, while the second account `b2` has an initial balance of `100.0` euros.  
- You can deposit an amount `x` into each account, respectively via `d1` for the first account and `d2` for the second.  
- Money can be withdrawn with `w1` for the first account and `w2` for the second.  
- When withdrawing, the account of type `1` (Basic) applies a withdrawal fee of 4 euros, while the account of type `2` (Standard) applies a fee of 2 euros.  
:::

2. **Quiz**: Which of the two accounts has the higher balance after these transactions?

3. Replace the previous transactions with the following:

```{code} cpp
d1(100.0);
w1(50.0);
d2(100.0);
w2(50.0);
```

4. **Quiz**: Now, which account has the higher balance?

:::{warning} Have you spotted the bug?  
:class: dropdown  
- Is the code logical?  
- What is the source of the accounting error?  
- Is it easy to add and perform transactions with a third account?  
- What improvements can be made to this code?  
:::

5. To improve this code, we will rewrite it as follows:

(tp2-ex2-class-example)=
```{code} cpp
#include <iostream>
using namespace std;

enum t {B, S};

class b {
private:
    double mB1;
    t mT;
    double mF;

public:
    b(double b1, t t1) : mB1(b1), mT(t1) {
        switch(t1){
            case B:
                mF = 4;
                break;
            case S: 
                mF = 2;
                break;
        }
    }

    void d(double x) {
        mB1 += x;
    }

    void w(double x) {
        if (x + mF <= mB1) {
            mB1 -= (x + mF);
        } else {
            cout << "Insufficient funds!" << endl;
        }
    }

    double getB1() {
        return mB1;
    }

    string tToString(){
        switch(mT){
            case B: return "Basic";
            case S: return "Standard";
            default: return "Unknown";
        }
    }
};

int main() {
    b a1(0.0,B);
    b a2(100.0,S);
    a1.d(100.0);
    a1.w(50.0);
    a2.d(100.0);
    a2.w(50.0);
    cout << "First account type: " << a1.tToString() << " and balance: " << a1.getB1() << endl;
    cout << "Second account type: " << a2.tToString() << " and balance: " << a2.getB1() << endl;
    return 0;
}
```
(ps2-class-members)=
:::{important} C++ Class Members  
Private members of a class in C++ are often prefixed with `m` (e.g., `mB1`, `mT`, and `mF`). This prefix helps distinguish them from local variables used in the constructor (e.g., `double b1`), which are used to initialize members like `mB1` (with `mB1(b1)`).
Other conventions exist, such as using the `_` prefix (e.g., `_b1`).

However, this practice is not always systematic. For example, it is possible to use *shadowing*, where a local variable and a class member share the same name. In this case, the distinction is made by using `this->variable` to refer to the class attribute and simply `variable` for the local variable.
In Java, shadowing is the most popular convention (more details on this in your Object-Oriented Development course).

In this course, we will adopt the prefix convention (either `m`, or `_`) to clarify the distinction between class members and other variables.  
You must stay consistent in your repo, **do not mix `m` and `_`**.
:::

(ps2-getter-setter)=  
:::{important} Getter and Setter  
**Encapsulation** is essential for protecting the sensitive data of your object.  
Therefore, it is important to declare the attributes of your class as `private`.  
As a result, it is necessary to use `get` (to access) and `set` (to modify) methods for these attributes.
These methods should not be defined systematically. Only define `get` methods for attributes that need to be accessed, and `set` methods for attributes that are allowed to be modified from outside the class.

The recommended naming convention for these methods is `getAttributeName` and `setAttributeName` (without the `m` prefix).  
:::

6. Do you understand what this code does?

(ps2-constructor)=
:::{hint} Hints
:class: dropdown
- The `t` object of type `enum` can take the values `B` (for "Basic") or `S` (for "Standard").
- The `b` class represents a bank account.
- It has the following attributes:
    - `mB1`, which represents its balance.
    - `mT`, which indicates its type (Basic or Standard).
    - `mF`, which is its withdrawal fee.

The following code is the constructor of the class:
```{code} cpp
b(double b1, t t1) : mB1(b1), mT(t1) {
    switch(t1){
        case B:
            mF = 4;
            break;
        case S: 
            mF = 2;
            break;
    }
}
```

- An object of class `b` is constructed with two arguments: `double b1`, which is its initial balance, and `t t1`, which defines the account type.
- The `switch(t1)` manages the different account types.
    - If `t1 == B`, the withdrawal fee is 4 euros.
    - If `t1 == S`, the withdrawal fee is 2 euros.
- The `tToString()` method displays the types as corresponding strings.
:::

7. **Quiz**: This time, which account has the highest balance?

8. This code compiles and works as it should, but it is a real eyesore. It hurts me physically to look at it. It is essential to rename these horrors.

Here's the translation of the provided text:

:::{hint} Did you complete your task?
:class: dropdown
- Did you correctly apply the **camelCase** convention?
- Did you follow the naming conventions for classes and methods? (The names of `enum` objects should also start with a capital letter.)
- Did you follow the conventions for using the `m` prefix and for getters and setters?
- Are there still variables, methods, or objects with names that consist of a single letter?
- Do meaningless numbers still appear in the names?
- Are the names you chose sufficiently descriptive of the intent behind each variable, method, or object?

If you have any doubts, feel free to refer back to the [course](../Cours/cm2.md).

If you think you've correctly renamed the elements, remember that a good programmer should be able to write the same code correctly from the start. Of course, one can always go back to improve the code, but the level of code presented in this exercise is simply unacceptable. 
By the end of this course, you should be able to name elements properly from the start.
In fact, I spent more time making this code functional but visually disastrous than writing it correctly from the start.
Inconsistent and vague names make the code hard to understand and call its logic into question.

I hope this exercise has convinced you of the importance of clean coding 😊.
:::

9. Recompile and test your code.

10. Add a third "Premium" type with a withdrawal fee of 1 euro.

11. In the `int main()` function, replace the existing code with the following instructions:
- Create three accounts with the three different types (Basic, Standard, and Premium), each having an initial balance of 100.0 euros.
- Make a deposit of 50 euros into each account.
- Make a withdrawal of 148 euros from each account.
- Display the information (types and balances) of the three accounts.

12. **Quiz**: What is the balance of each account?

Here is the translation of the provided text:

Return to the [objectives](#ps2-objectives) and check the points you have mastered. Review the points you haven't fully understood yet. Call your supervisor if needed.

:::{important} Git
:class: dropdown
At the end of this assignment, your remote repository should be structured as follows:
- At the root, you will find the folders `PS1/` and `PS2/`, along with the files `.gitignore` and `README.md`. You can also see the configuration folder `.git` if you are displaying hidden folders.
- The `PS1/` folder should contain the files `hello-world.cpp` and `my-first-file.txt`.
- The `PS2/` folder should contain the files `classes-and-methods.cpp` and `magic-numbers.cpp`.

Your working directory may contain ignored files, but make sure to check that your GitLab repository is clean, as it will be evaluated at the end of the course!

If this is not the case, go back to [the end of PS1](#ps1-to-finish), where additional instructions have been provided to help you clean your remote repository.
:::