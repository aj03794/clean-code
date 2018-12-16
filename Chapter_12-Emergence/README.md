#### Emergence

######

- According to Kent Beck, a design is `simple` if it follows these rules
    * Runs all tests
    * Contains no duplication
    * Expresses the intent of the programmer
    * Minimizes the number of classes and methods
    (In order of importance)

###### Simple Design Rule 1: Runs All the Tests

- A design must produce a system that acts as intended
- A system might have a perfect design on paper, but if there is no simple way to verify that the system actually works as intended, then all the paper effort is questionable

- A system that is comprehensively tested and passes all of its tests all of the time is a testable system
- Systems that aren't testable aren't verifiable

- Making our system testable pushes us toward a design where our classes are small and single purpose
- Easier to test classes that conform to `single responsibility principle`
- The more tests we write, the more we'll continue to push toward things that are simpler to test
- Making sure our system si fully testable helps us create better designs

- Tight coupling makes it difficult to write tests
- So the more tests we write, the more we use principles like DIP and tools like dependency injection, interfaces, and abstraction to minimize coupling

- Helps us towards goal of low coupling and high cohesion

###### Simple Design Rules 2-4: Refactoring

- Once we have tests, we are empowered to keep our code and classes clean
- Do this by incrementally refactoring the code
- For each few lines of code we write, reflect on the new design
- If we degraded it, clean it up and run tests to demonstrate that we haven't broken anyhting
- `The fact that we have these tests eliminiates the fear that cleaning up the code will break it`

- During this refactoring step, you can apply anything about good software design
- Increase cohesion, decrease couping, separate concerns, modularize software concerns, shrink our functions and classes, choose better names, and so on
- This is where we apply the final three rules of simple design
    * Eliminate duplication
    * Ensure expressiveness
    * Minimize the number of classes and methods

###### No Duplication

- Duplication is the primary enemy of a well-designed system
- Represents additional work, additional risk, and additional unnecessary complexity
- Lines of code that are similar can often be massaged to look even more alike so that they can be easily refactored
- Duplication can exist in many other forms such as duplication of implementation
- For example, might have two methods in a collection class

```
int size() {}
boolean isEmpty() {}
```

- Could have separate implementations for each method
- `isEmpty` could track a boolean, while `size` could track a counter
- Can eliminate this duplication by tying `isEmpty` to the definition of `size`

```
boolean isEmpty() {
    return 0 == size();
}
```

- Creating a clean system requires the will to eliminate duplication, even in just a few lines of code
- For example, consider following code

```
public void scaleToOneDimension(float desiredDimension, float imageDimension) {
    if (Math.abs(desiredDimension - imageDimension) < errorThreshold)
        return;
    float scalingFactor = desiredDimension / imageDimension;
    scalingFactor = (float)(Math.flow(scalingFactor * 100) * 0.01f);

    RenderedOp newImage = ImageUtilities.getScaledImage(image, saclingFactor, scalingFactor);
    image.dispose();
    System.gc();
    image = newImage;
}

public synchronized void rotate(int degrees) {
    RenderedOp newImage = ImageUtilities.getRotatedImage(image, degrees);
    image.dispose();
    System.gc();
    image = newImage;
}
```

- To keep this system clean, we should eliminate the small amount of duplication btwn `scaleToOneDimension` and `rotate` methods

```
public void scaleToOneDimension(float desiredDimension, float imageDimension) {
    if (Math.abs(desiredDimension - imageDimension) < errorThreshold)
        return;
    float scalingFactor = desiredDimension / imageDimension;
    scalingFactor = (float)(Math.flow(scalingFactor * 100) * 0.01f);

    replaceImage(ImageUtilities.getScaledImage(image, scalingFactor, scalingFactor));
}

public synchronized void rotate(int degrees) {
    replaceImage(ImageUtilities.getRotatedImage(image, degrees));
}

public void replaceImage(RenderedOp newImage) {
    image.dispose();
    System.gc();
    image = newImage;
}
```

- As we extract commonality at this very tiny level, start to recognize violations of SRP
- Might move a newly extracted method to another class
- This `reuse in the small` can cause system complexity to shrink dramatically
- Understanding how to achieve `reuse in the small` is essential to achieving reuse in the large

- The `TEMPLATE METHOD` pattern is a common technique for removing higher-level duplication
- For example:

```
public class VacationPolicy {

    public void accrueUSDivisionVacation() {
        // code to calculate vacation based on hours worked to date\
        // ...
        // code to ensure vacation meets US minimums
        // ...
        // code to apply vacation to payroll record
        // ...
    }

    public accrueEUDivisionVacation {
        // code to calculate vacation based on hours worked to date
        // ...
        // code to ensure vacation meets EU minimums
        // ...
        // code to apply vacation to payroll record
        // ...
    }

}
```

- The code across `accrueUSDivision` and `accrueEUDivisionVacation` is largely the same, with the exception of calculating legal minimums
- That bit of the algorithm changes based on the employee type

- We can eliminate obvious duplication by apply the `TEMPLATE METHOD` pattern

```
abstract public class VacationPolicy {
    public void accrueVacation() {
        calculateBaseVacationHours();
        alterForLegalMinimums();
        applyToPayroll();
    }

    private void calculateBaseVacationHours() { ... };
    abstract protected void alterForLegalMinimums();
    private void applyToPayroll() { ... };

}

public class USVacationPolicy extends VacationPolicy {
    @Override protected void alterForLegalMinimums() {
        // US specific logic
    }
}

public class EUVacationPolicy extends VacationPolicy {
    @Override protected void alterForLegalMinimums() {
        // US specific logic
    }
}

```

- Subclasses fill in the `hole` in the `accrueVacation` algorithm, suppliying only bits of information that are not duplicated

###### Expresssive

- Easy to write code that `we` understand, b/c at the time we write it we're deep in an understanding of the problem we're trying to solve
- Other maintainers of the code aren't going to have so deep an understanding

- Majority of the cost of a software project is in long-term maintenance
- In order to minimize the potential for defects as we introduce change, it's critical for us to be able to understand what a system does
- As systems become more complex, they take more and more time for a developer to understand, and there is an even greater opportunity for a misunderstanding
- Code should clearly express the intent of its author
- The clearer the author can make the code, the less time others will have to spend understanding it
- This will reduce defects and shrink cost of maintenance

- You can express yourself by choosing good names
- Want to be able to hear a class or function name and not be surprised when we discover its responsibilities

- Can also express yourself by keeping your functions and classes small
- Small classes and functions are usually easy to name, easy to write, and easy to understand

- Can also express yourself by adding standard nomenclature
- Design patterns are largely about communication and expressiveness
- By using standard pattern names such as `COMMAND` or `VISITOR` in the names of the classes that implement those patterns, you can describe your design to other developers

- Well-written unit tests are also expressive
- Primary goal of tests is to act as documentation by example
- Someone reading our tests should be able to get a quick understanding of what a class is all about

- Most important way to be expressive is to `try`
- All too often we get our code working and then move on to the next problem w/o giving sufficient thought to making that code easy for the next person to read
- Remember, the most likely next person to read the code will be you

###### Minimal Classes and Methods

- In an effort tom make our classes and methods small, we might create too many tiny classes and methods
- Try and keep functions and class counts `low`

- High class and method counts are sometimes the result of pointless dogmatism
- Consider, for example, a coding standard that insists on creating an interface for each and every class
- Or consider devs who insist that fields and behaviors must always be separated into data classes and behavior classes
- Such dogma should be resisted and a more pragmatic approach adopted

- Goal is to keep our overall ssytem small while we are also keeping our functions and classes small
- Remember, however, that this rule is the lowest priority of the 4 rules of simple design
- Although it's important to keep class and function count low, it's more important to have tests, eliminate duplication, and express yourself