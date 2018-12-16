#### Meaningful Names

###### Use Intention-Revealing Names

- Names should reveal intent
- Choosing good names takes time but saves more than it takes
- Take care with your names and change them when you find better ones
- Everyone who reads your code will be happier if you do

- The name of a variable, function, or class, should answer all the big questions
- It should tell you why it exists, what it does, and how it is used
- `If a name requires a comment, then the name does not reveal its intent`

`int d; //elapsed time in days`

- Here the name `d` reveals nothing
- We should choose a name that specifies what is being measured and the unit of measurement

```
int elapsedTimeInDays;
int daysSinceCreation;
int daysSinceModification;
int fileAgeInDays
```

- Choosing names that reveal intent can make it much easier to understand and change code
- What is the purpose of this code?

```
public List<int[]> getThem() {
    List<int[]> list1 = new ArrayList<int[]>();
    for (int [] x: theList)
        if (x[0] == 4)
            list1.add(x);
        return list1;
}
```

- Why is it so hard to tell what this code is doing?
- No complex expressions
- Spacing and indentation are reasonable
- Only 3 variables and 2 constants mentioned

- The problem isn't the simplicity of the code but the `implicity` of the code: the degree to which the context is not explicit in the code itself
- The code implicity requires that we know the answers to questions such as:

1. What kinds of things are in theList?
2. What is the significance of the zeroth subscript of an item in theList?
3. What is the significance of the value 4?
4. How would I use the list being returned?

- The answers to these questions are not present in the code sample, `but they could have been`
- Say we're working in a mine sweeper game
- Find that the board is a list of cells called `theList`
- Let's rename that to `gameBoard`

- Each cell on the board is represented by a simple array
- Further find that the zeroth subscript is the location of a status value and that a status value of 4 means `flagged`
- Just by giving these concepts names, we can improve the code considerably

```
public List<int[]> getFlaggedCells() {
    List<int[]> flaggedCells = new ArrayList<int[]>();
    for (int [] cell: gameBoard)
        if (x[STATUS_VALUE] == FLAGGED)
            flaggedCells.add(cell)
        return flaggedCells;
}
```

- Note that the simplicity of the code has not changed
- Still has exactly the same number of operators and constants, w/ exactly the same number of nesting levels
- But the code has become much more explicit

- Can go further and write a simple class for cells instead of using an array of `ints`
- It can include an intention-revealing function (call it `isFlagged`) to hide the magic numbers
- It results in a new version of the function

```
public List<int[]> getFlaggedCells() {
    List<int[]> flaggedCells = new ArrayList<int[]>();
    for (Cell cell: gameBoard)
        if (cell.isFlagged())
            flaggedCells.add(cell)
        return flaggedCells;
}
```

- With these simple name changes, it's not difficult to understand what's going on
- This is the power of choosing good names

###### Avoid Disinformation

- Programmers must avoid leaving false clues that obscure the meaning of code
- Should avoid words whose entrenched meanings vary from our intended meaning

- Do not refer to a grouping of accounts as an `accountList` unless it's actually a `List`
- The word list means something specific to programmers
- If the container holding the accounts is not actually a `List`, it may lead to false accusations
- So `accountGroup` or `bunchOfAccounts` or just plain `accounts` would be better

- Beware of using names which vary in small ways
- `XYZControllerForEfficientHandlingOfStrings` and `XYZControllerForEfficientStorageOfStrings` are hard to tell apart

- Spelling similar concepts similarly is `informative`
- Using inconsistent spellings is `disinformation`
- It is very helpful if names for similar things sort together alphabetically and if the differences are very obvious, b/c th edev is likely to pick an object by naming w/o seeing your copious comments or even the list of methods supplied by that class

###### Make Meaningful Distinctions

- If names must be different, then they should also mean something different
- Consider the following

```
public static void copyChars(char a1[], char a2[]) {
    for (int i = 0; i < a1.length; i++) {
        a2[i] = a1[i];
    }
}
```

- This function reads much better when `source` and `destination` are used for the argument names

- Noise words are another meaningless distinction
- Imagine that you have a `Product` class
- Have another called `ProductInfo` or `ProductData`, you hav emade the names different w/o them meaning anything different
- `Info` and `Data` are indistinct noise words like `a`, `an`, and `the`

- Nothing wrong with using prefix conventions like `a` and `the` so long as they make a meaningful distinction
- For example, you might use `a` for all local variables and `the` for all function arguments
- The problem comes in when you decide to call a variable `theZork` becaus you have another variable named `zork`

###### Use Pronounceable Names

- If you can't pronounce then word then you can't discuss it without sounds like an idiot
- This matters because programming is a social activity

- If a developer has to have variables explained to them, the variable is a bad name

###### Use Searchable Names

- Single letter names and numeric constants have a particular problem in that they are not easy to locate across a body of text

- Single-letter names should ONLY be used as local variables inside short methods
- `The length of a name should correspond to the size of its scope`
- If a variable or constatnt might be seen or used in multiple places in a body of code, it is imperative to give it a search-friendly name

###### Avoid Mental Mapping

- Readers shouldn't have to mentally translate your names into other names they already now
- This problem generally arises froma choice to use neither problem domain terms nor solution domain terms

- `Clarity is king`

###### Class Names

- Classes and objects hould have noun or noun phrase names like `Customer`, `WikiPage`, `Account`, and `AddressParser`
- Avoid words like `Manager`, `Processor`, `Data`, or `Info` in the name of the class
- A class name should not be a verb

###### Method Names

- Methods should have verb or verb phrase names like `postPayment`, `deletePage` or `save`
- Accessors, mutators, and predicates should be named for their value and prefxied with `get`, `set`, and `is`

```
string name = employee.getName();
customer.setName("mike");
if (paycheck.isPosted())...
```

- When constructors are overloaded, use static factory methods with names that describe the arguments

```
Complex fulcrumPoint = Complex.fromRealNumber(23.0);
```

- is generally better than

```
Complex fulcrumPoint = new Complex(23.0);
```

- Consider enforcing their use by making the corresponing constructors prrivate

###### Don't Be Cute

- If names are too clever, they will be memorable only to people who share the author's sense of humor, and only as long as these people remember the joke

###### Pick One Word per Concept

- Pick one word for one abstract concept and stick with it
- For instance, it's confusing to have `fetch`, `retrieve`, and `get` as equivalent methods of different classes
- How doy ou remember which method name goes with which class?

- Likewise it's confusing to have a `controller`, `manager`, and `driver` in the same code base
- What is the essential different between a `DeviceManager` and a `ProtocolController`
- Why are they both not `controllers` or both not `managers`

- A consistent lexicon is very helpful to programmers who must use your code

###### Don't Pun

- Avoid using the same name for two purposes
- Using the same term for two different ideas is usually a pun

- if you follow the `one word per concept` rule, you could end up with many classes that have an `add` method
- As long as the parameter lists and return values of the various `add` methods are semantically equivalent, all is well

- However, one might decide to use the word `add` for `consistency` when he/she is not in fact adding in the same sense
- Let's saw we have many classes where `add` will create a new value by adding or concatenating two existing values
- Now let's say we are writing a new class that has a method that puts its single parameter into a collection
- Should we call this method `add`?
- It might seem consistent, but the semantics are different, so we should use a name like insert or append instead

- Our goal is to make our code as easy to understand as possible
- We want our code to be a quick skim, not an intense study

###### Use Solution Domain Names

- Use CS terms, algorithm names, pattern names, math terms, and so forth
- Not wise to draw `every` name from the problem domain b/c we don't want our coworkers to run back and forth to the customer asking what every name means when they alrady know the concept by a different name

###### Use Problem Domain Names

- When there is not `programmer-eese` for what you're doing, use the name from the problem domain

- Separating solution and problem domain concepts is part of the job of a good programmer and designer

###### Add Meaningful Context

- There are few names which are meaningful in and of themselves - most are not
- Instead you need to place names in context for your reader by enclosing them all in well-named classes, functions, or namespaces
- When all else fails, then prefixing the name may be necessary as a last resort