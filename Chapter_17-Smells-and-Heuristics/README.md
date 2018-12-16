##### Environment

######## Build Requires More Than One Step
- Building a project should be a trivial operation
- You should not have to check many little pieces out from source control
- Should not need a sequence of arcane commands or context dependent scripts in order to build the individual elements

######## Tests Require More Than One Step

- You should be ble to run all the unit tests with just one command
- In the best case you can run all the tests by clicking on one button in your IDE
- In the worst case you should be able to issue a single simple command in a shell
- Being able to run all the tests is so fundamental and so important that it should be quick, easy, and obvious to do

##### Functions

######## Too Many Arguments

- Functions should have a small number of arguments
- No argument is best, followed by 1, 2, and 3

######## Flag Arguments

- Boolean arguments loudly declare that the function does more than one thing
- They are confusing and should be eliminated

##### General

######## Multiple Languages in One Source File

- Today's modern programming environments make it possible to put many different languages into a single source file

- The ideal is for a source file to contain one, and only one, language
- Realistically, we may have to use more than one
- But we should take pains to minimize both the number and extend of extra languages in our source files

######## Obvious Behavior is Unimplemented

- Following `The Principle of Least Surprise`, any function or class should implement the behaviors that anotehr programmer could reasonably expect

- When obvious behavior is not implemented, readers and users of the code can no longer depend on their intuition about function names
- They lose their trust in the original author and must fall back on reading the details of the code

######## Incorrect Behavior at the Boundaries

- It seem obvious to say that code should behave correctly
- The problem is that we seldom realize just how complicated correct behavior is
- Devs often write functions that they think will work, and then trust their intuition rather than going that their code works in all corner and boundary cases

######## Overridden Safeties

- It is risky to override safeties
- Turning off warnings may help you get your build to succeed, but at the risk of endless debugging sessions
- Turning off failing tests and telling yourself you'll get them to pass later is bad

######## Duplication

- Every time you see duplication in the code, it represents a missed opportunity for abstraction
- The duplication could probably become a subroutine or perhaps another class outright
- By folding duplication into such an abstraction, you increase the vocabulary of the language of your design
- Other programmers can use the abstract facilities you create
- Coding becomes faster and less error prone b/c you have raised the abstraction level

- Subtle form is the switch/case or if/else chain that appears again and again in various modules, always testing for the same conditions
- These should be replaced with polymorphism

- More subtley are the modules that have similar algorithms, but that don't share similar lines of code
- This is still duplication and should be addressed by using the `TEMPLATE METHOD` or `STRATEGY` pattern

######## Code at Wrong Level of Abstraction

- It is important to create abstractions that separate higher level general concepts from lower level detailed concepts
- Sometimes we do this by creating abstract classes to hold higher level concepts and derivatives to hold the lower level concepts
- When we do this, we need to make sure that the separation is complete
- We want `all` the lower level concepts to be in the derivatives and `all` higher level concepts to be in the base class

- For example, constants, variables, or utility functions that pertain only to the detailed implementation should not be present in the base class
- The base class should know nothing about them

- This rule also pertains to source files, components, and modules
- Good software design requires that we separate concerns at different levels and place them in different containers
- Sometimes these containers are base classes or derivatives and sometimes they are source files, modules, or components
- Whatever the case, the separation needs to be complete
- Don't want lower and higher level concepts mixed together

- Consider the following code:

```
public interface Stack {
    Object pop() thows EmptyException;
    void push(Object o) thows FullException;
    double percentFull();
    class EmptyException extends Exception {}
    class FullException extends Exception {}
}
```

- The `percentFull` function is at the wrong level of abstration
- Although there are many implementations of `Stack` where the concept of fullness is reasonable, there are other implementations that simply could not know how full they are
- So the function would be better placed in a derivative interface such as `BoundedStack`

- Isolation abstraction is one of the hardest things that software developers do, and there is no quick fix when you get it wrong

######## Base Classes Depending on Their Derivatives

- Most common reason for paritioning concepts into base and derivative classes is so that the higher level base class concepts can be independent of the lower level derivative class concepts
- Therefore when we see base classes mentioning the names of their derivatives, we suspect a problem
- In general, base classes should know nothing about their derivatives
- They should be independently deployable

######## Too Much Information

- Well-defined modules have very small interfaces that allow you to do a lot with a little
- Poorly defined modules have wide and deep interfaces that force yout o use many different gestures to get simple things done
- A well-defined interface does not offer very many functions to depend upon, so coupling is low
- A poorly defined interface provides lots of functions that you must call, so coupling is high

- Good software devs learn to limit what they expose at the interface of their classes and modules
- They have a fewer methods a class has, the better
- The fewer variables a functions know about, the better
- The few instance variables a class has, the better

######## Dead Code

- Dead code is code that isn't executed
- You find it in the body of an `if` statement that checks for a condition that can't happen
- Find it in the `catch` block of a `try` that never `throws`

- Dead code is their b/c is is not completely updated when designs change
- It was written at a time when the system was `different`
- When you find dead code, delete it from the system

######## Vertical Separation

- Variables and functions should be defined close to where they are used
- Local variables should be declared just above their first usage and should have a very small vertical scope
- Don't want local variables declared hundreds of lines distant from their usage

- Private functions should be defined just below their first usage
- Private functions belong to the scope of the whole class
- But we'd like to limit the vertical distance btwn the invocations and definitions
- Fnding a private function should just be a matter of scanning downward from the first usage

######## Inconsistency

- If you do something a certain way, do all similar things in the same way
- This goes back to the principle of least surprise
- Be careful with the conventions you choose, and once chosen, be careful to continue to follow them
- If within a particular functiion you use a variable named `response` to hold an `HttpServletResponse` objects
- If you name a method `processVerificationRequest` then use a similar name, such as `processDeletionRequest`, for the methods that process other kinds of reuest

- Simple consistency like this can make code much easier to read and modify

######## Clutter

- What use is a default constructor with no implementation?
- All it serves to do is clutter up the code with meaningless variables
- Variables that aren't used, functions that are never called, comments that add no information, and so forth
- All these things are clutter and should be removed
- Keep your source files clean, well organized, and free of clutter

######## Artifical Coupling

- Things that don't depend each other should not be artifically coupled
- For example, general `enums` should not be contained within more specific classes b/c this forces the whole application to know about these specific classes
- The same goes for general purpose functions being declared in specific classes

######## Selector Arguments

- Nothing more abominable than a dangle `false` argument at the end of a function call
- What does it change if it were `true`
- Not only is the purpose of a selector argument difficult to remember, each selector argument combines many functions into one
- Selector arguments are just a lazy way to avoid splitting a large function into several smaller functions

Consider:

```
pubic int calculateWeeklyPay(boolean overtime) {
    int tenthRate = getTenthRate();
    int tenthsWorked = getTenthsWorked();
    int straightTime = Math.min(400, tenthsWorked);
    int overTime = Max.max(0, tenthsWorked - straightTime);
    int straightPay = straightTime * tenthRate;
    double overtimeRate = overtime ? 1.5 : 1.0 * tenthRate;
    int overtimePay = (int)Math.round(overTime * overtimeRate);
    return straightPay + overtimePay;
}
```

- You can call this function with a `true` if overtime is paid as time and a half, and with a `false` if overtime is paid as straight time
- It's bad enough that you must remember what `calculateWeeklyPay(false)` means whenever you happen to stumble across it
- But the real shame of a function like that is the author missed the opportunity to write the following

```
public int straightPay() {
    return getTenthsWorked() * getTenthRate();
}

public int overTimePay() {
    int overTimeTenths = Math.max(0, getTenthsWorked() - 400);
    int overTimePay = overTimeBonus(overTimeTenths)
    return straightPay() + overTimePay();
}

private int overTimeBonus(int overTimeTenths) {
    double bonus = 0.5 * getTenthRate() * overTimeTenths;
    return (int) Math.round(bonus);
}
```

- Selectors need not be `boolean`
- They can be enums, integers, or any other type of argument that is used to select the behavior of the function
- In general it is better to have many functions than to pass code into a function to select behavior

######## Obscured Intent

- Want code to be expressive as possible
- Here is the `overTimePay` function as it might have appeared

```
public int m_otCalc() {
    return iThsWkd * iThsRte + (int)Math.round(0.5 * iThsRate * Math.max(0, isThsWkd - 400));
}
```

- This is very hard to understand
- Worth taking time to make the intent of our code visible to our readers

######## Misplaced Responsibility

- One of the most important decisions a softwre dev can make is where to put code
- Where should the `PI` constant go
- Should it be in the `Math` class
- Perhaps it belongs in the `Trigonometry` class
- Maybe in the `Circle` class

- Principle of least surprise comes into play here
- could should be place where a reader would naturally expect it to be
- The `PI` constant should go where the trig functions are declared
- The `OVERTIME_RATE` constant should be declared in the `HourlyPayCalculator` class

Page 296