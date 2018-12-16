#### 2 Meaningful Names

- Names are everywhere in software

######## Use Intention-Revealing Names

- Should use names that reveal intent
- Choosing good names takes time but saves more than it takes

- The name of a variable, function, or class, should answer all the big questions
- Should tell you why it exists, what it does, and how it is used
- If a name requires a comment, then the name does not reveal its intent

- The name `d` reveals nothing
- Does not evoke a sense of elaapsed time, not of days
- Shoudl choose a name that specifies what is being measured and the unit of measurement

```
const elapsedTimeInDays
const daysSinceCreation
const daysSinceModification
const fileAgeInDays
```

- Choosing names that reveal can make it much easier to understand and change code
- What is the purpose of this code?

```
public List<int[]> getThem() {
     List<int[]> list1 = new ArrayList<int[]>();
     for (int[] x : theList)
       if (x[0] == 4)
         list1.add(x);
     return list1;
   }
Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- Why is it hard to tell what this code is doing?
- No complex expressions
- Spacing and indentation are reasonable
- There are only 3 variables and two constants mentioned
- Aren't even fancy classes or polymorphic methods, just a list of arrays (or so it seems)

- Problem isn't the simplicity of the code, but the `implicity` of the code: the degree to which the context is not explicit to the code itself
- The code implicitly requires taht we know the answers to the questions such as:
    1. What kinds of things are in `theList`?
    2. What is the significance of the zeroth subscript of an item in `theList`
    3. What is the significance of the value 4?
    4. How would I use the list being returned?

- The answers to these questions are not present in the code sample, `but they could have been`
- Say we're working on a mine sweeper game
- We find that the board is a list of cells called `theList`
- Let's rename that to `gameBoard`

- Each cell on the board is represented by a simple array
- We further find that the zeroth subscript is the location of a status value and that a status value of 4 means `flagged`
- Just by giving these concepts names we can improve the code considerably

```
public List<int[]> getFlaggedCells() {
     List<int[]> flaggedCells = new ArrayList<int[]>();
     for (int[] cell : gameBoard)
      if (cell[STATUS_VALUE] == FLAGGED)
        flaggedCells.add(cell);
     return flaggedCells;
}

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- Simplicity of the code has not changed
- Code has become much more explicit

- We can go further and write a simple class for cells instead of using an array of ints
- It can include an intention reveal function (call it isFlagged) to hide the magic numbers
- It results in a new version of the function

```
public List<Cell> getFlaggedCells() {
     List<Cell> flaggedCells = new ArrayList<Cell>();
     for (Cell cell : gameBoard)
       if (cell.isFlagged())
         flaggedCells.add(cell);
     return flaggedCells;
   }

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

######## Avoid Disinformation

- Programmers must avoid leaving false clues that obscure the meaning of the code
- Should avoid words whose entrenched meanings vary from our intended meaning 
- For example `hp`, `aix`, `sco` would be poor variable names b/c they are names of Unix platforms or variants
- Even if you are coding a hypotenuse and `hp` looks like a good abbreviation, it could be disinformation

- Do not refer to a grouping of accounts as an `accountList` unless it's actually a `List`
- The word lsit means something specific to programmers
- If the container holding the accounts is not actually a `List`, it may lead to false conclusions
- So `accountGroup` or `bunchOfAccounts` or just plain `accounts` would be better

- Beware of names which vary in small ways
- How long does it take to spot the subtle difference btwn a `XYZControllerForEfficientHandlingOfStrings` in one module, and somewhere a little more distant, `XYZControllerForEfficientStorageOfStrings`?
- The words have very similar shapes

- Spelling similar concepts similarly is `information`
- Using inconsistent spelling is `disinformation`
- W/ automatic code completion, it is very helpful if names for very similar things sort together alphabetically and if the differences are very obvious, b/c the dev is likely to pick an object by name w/o seeing your copious comments or even the list of methods supplied by that class

- A truly awful example of disinformative names would be the use of the lower-case `L` or uppercase `o` as variable names, especially in combinatin
- The problem is that they look almost entirely like the constants one and zero

```
int a = l;
   if ( O == l )
     a = O1;
   else
     l = 01;

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- May seem like a contrivance, but this sort of code can be abundant
```
Contrivance: the use of skill to bring something about or create something.
```
- In one case the author of the code suggested using a different font so that the differences were more obvious, a solution that would have to be passed down to all future developers as oral tradition or in a written document

######## Make Meaningful Distinctions

- Programmers create problems for themselves when they code solely to satisfy a compiler or interpreter 
- For example, b/c you can't use the same name ot refer to two different things in the same scope, you might be tempted to change one name in an arbitrary way
- Sometimes this is done by misspelling one, leading to the where correcting spelling errors leads to an inability to compile

- Not sufficient to add number series or noise words even though the compiler is satisfied
- If names must be differnet, then they should also mean something different

- Number-series naming `(a1, a2, ... aN)` is the opposite of intentional naming
- Such names are not disinformative - they are noninformative
- They provide no clue to the author's intention

Consider 
```
“public static void copyChars(char a1[], char a2[]) {
     for (int i = 0; i < a1.length; i++) {
       a2[i] = a1[i];
     }
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```
- The above function reads much better when `source` and `destination` are used for argument names

- Noise words are another meangingless dinstinction
- Imagine `Product` class
    * If you have another called `ProductInfo` or `ProductData`, you have made the names different w/o making them mean anything different
    - `Info` and `Data` are indistinct noise words like `a`, `an`, and `then`

- Ther is nothing wrong with using prefix conventions like `a` and `the` as they make a meaningful distinction
- For example, might use `a` for all local variables and `the` for all function arguments
- Prob is when you make a variable called `theZork` b/c you already have naother variable called `zork`

- Noise words are redundant
- Word `variable` should never appear in a variable name
- Word `table` should never appear in a table name

###### Use Pronounceable Names

- Compare these two code snippets

```
“class DtaRcrd102 {
     private Date genymdhms;
     private Date modymdhms;
     private final String pszqint = ”102”;
      /* … */
   };”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

```
“ class Customer {
     private Date generationTimestamp;
     private Date modificationTimestamp;;
     private final String recordId = ”102”;
     /* … */
   };”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- Second is much more readable just because of the pronounceable names
- Makes conversation easier

###### Use Searchable Names

- Single-letter names and numeric constants have a particular in that they are not easy to locate across a body of text

- One could easily grep for `MAX_CLASSES_PER_STUDENT` but the number 7 could be more troublesome

- Single-letter names can only be used as local variables inside `short scopes`
- `The length of a name should correspond to the size of its scope`
- If a variable/constant might be seen or used in multiple places in a body of code, it is worthwhile to give it a search friendly name

- Compare two code snippets
```
“for (int j=0; j<34; j++) {
     s += (t[j]*4)/5;
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

```
int realDaysPerIdealDay = 4;
   const int WORK_DAYS_PER_WEEK = 5;
   int sum = 0;

for (int j=0; j < NUMBER_OF_TASKS; j++) {
     int realTaskDays = taskEstimate[j] * realDaysPerIdealDay;
     int realTaskWeeks = (realdays / WORK_DAYS_PER_WEEK);
     sum += realTaskWeeks;
   }

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 

```

- `sum` is not particularly useful name but it is searchable
- Intentionally named code makes for a longer function, but will be much easier to find `WORK_DAYS_PER_WEEK` than to find all places `5` was used and find the instance you want

###### Avoid Encodings

- Encoding type or scope info adds an extra burden of deciphering

######## Interfaces and Implementations
- Avoid putting `I` before name for interfaces

###### Avoid Mental Mapping

- Readers shouldn't have to mentally translate your names into other names they already know
- This problem generally arises from a choice to use neither problem domain terms nor solution domain terms

- In most contexts (besides small for loops) - the single-letter name is a poor choice
- Just a place holder that the reader must mentally map to an actual concept
- No worse reason for using the name `c` than because `a` and `b` were already taken
- `clarity is king`

###### Class Names

- Classes and object should have a noun or noun phrase names like `Customer`, `WikiPage`, `Account`, and `AddressParser`
- Avoid words like `Manager`, `Processor`, Data, or `Info`
- A class name should not be a verb

###### Method Names

- Methods should have very or verb phrase names like `postPayment`, `deletePage`, or `save`
- Accessor, mutators, and predicates should be named for their value and prefixed with `get`, `set`, and `is` according to the `javabean standard`

```
“string name = employee.getName();
   customer.setName(”mike”);
   if (paycheck.isPosted())…”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- When constructors are overloaded, use static factory methods with names that describe the arguments

```
“Complex fulcrumPoint = Complex.FromRealNumber(23.0);

is generally better than

Complex fulcrumPoint = new Complex(23.0);

Consider enforcing their use by making the corresponding constructors private.”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

###### Don't Be Cute

- If names are too clever, they will only be memorable only to certain people
- Instead of `HolyHandGrenade` maybe `DeleteItems` is a better name
- Choose clarity over entertainment value

- `Say what you mean. Mean what you say`

###### Pick One Word per Concept

- Pick one word for one abstract oncept and stick with it
- For instance, it's confusing to have `fetch`, `retrieve`, and `get` as equivalent methods of different classes

- Function names have to be consistent in order for you to pick the correct method w/o any additional exploration

- It's confusing to have a `controller` and a `manager` and a `driver` in the same code base
- What is the difference between `DeviceManager` and a `ProtocolController`
- Why are both not `controllers` or both not `managers`
- The name leads you to expect two different objects that have very different type as well as having different classes

- Consistent lexicon (vocabulary) is important

###### Don't Pun

- Avoid using the same word for two purposes
- Using same term for two different ideas is essentially a pun

- If you follow the `one word per concept` rule, you could end up with many classes that have an `add` method
- As long as the parameter lists and return values of the various `add` methods are semantically equivalent, all is well

- However, one might decide to use the word `add` for `consistency` when he/she is not in fact adding in the same sense
- Example: we have many classes where `add` will create a new value by adding or concatenating two existing values.
- Now let's say we are writing a new class that has a method that puts its single parameter into a collection
- Should we call this method `add`?
- It might seem consistent b/c we have so many other `add` methods, but in this case, the semantics are different, so we should use a name like `insert` or `append` instead
- To call the new method `add` would be a pun

- Our goal, as authors of the code, is to make our code as easy as possible to understand
- Use model that it is our responsibility for making ourselves clear
- Reader should not have to dig out meaning

###### Use Solution Domain Names

- Remember that the people who readd your code will be programmers
- Go ahead and use CS terms, algorithm names, pattern names, math terms, and so forth
- Not wise to draw every name from the problem domain b/c we don't want our coworkers to have to ask what every name means when they already know the concept by a different name

- The name `AccountVisitor` means a great deal to a programmer who is familiar with the `Visitor` pattern
- Choosing technical names for technical things is usually the appropriate course

###### Use Problem Domain Names

- When no programmer name exists for what you're doing, use the name from mthe problem domain
- At least programmer who maintains code can ask a domain expert what it means

- Separating solution and problem domain concepts is part of the job of a good programmer and designer
- The code that has more to do with problem domain concepts should have names drawn from the problem domain

###### Add Meaningful Context

- There are a few names which are meaningful in and of themselves - most are not
- Instead, you need to place names in context for your reader by enclosing them in well-named functions, classes, and namespaces
- When all else fails, then prefixing the name may be necessary as a last resort

- Imagine that you have variables named `firstName`, `lastName`, `street`, `houseNumber`, `city`, `state`, and `zipcode`
- Taken together, it's clear they form an address
- But if you saw `state` variable being used alone in a method? Would you automatically infer that it was part of an address?

- You can add context by using prefixes: `addrFirstName`, `addrLastName`, `addrState`, and so on
- At least readers will understand that these variables are part of a larger structure
- Of course, a better solution is to create a class named `Address`
- Then, even the compiler knows that the variables belong to a bigger concept

- Consider method in Listing 2-1
- Do variables need a more meaningful context?
- Function name provides only part of the context; the algorithm provides the rest
- Once you read through the function, you see that the 3 variables, `number`, `verb`, and `pluralModifier` are part of the `guess statistics` messages
- Unfortunately, context must be inferred
- When you first look at the method, the meanings of the variables are opaque

```
“Listing 2-1 Variables with unclear context.
   private void printGuessStatistics(char candidate, int count) {   String number;
       String verb;
       String pluralModifier;
       if (count == 0) {
         number = ”no”;
         verb = ”are”;
         pluralModifier = ”s”;
       } else if (count == 1) {
         number = ”1”;
         verb = ”is”;
         pluralModifier = ””;
       } else {
         number = Integer.toString(count);
         verb = ”are”;
         pluralModifier = ”s”;
       }
       String guessMessage = String.format(
         ”There %s %s %s%s”, verb, number, candidate, pluralModifier
       );
       print(guessMessage);
     }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- The function is a little too long and the varibles are used throughout
- To split the functions into smaller pieces, we need to create a `GuessStatisticsMessage` class and make 3 variable fields of this class
- This provides a clear context for the 3 variables
- They are `definitively` part of the `GuessStatisticsMessage`
- The improvement of context allows the algorithm to be made much cleaner by breaking it into many smaller functions (Listing 2-2)

```
“Listing 2-2 Variables have a context.
   public class GuessStatisticsMessage {
     private String number;
     private String verb;
     private String pluralModifier;
   
     public String make(char candidate, int count) {
       createPluralDependentMessageParts(count);
        return String.format(
          "There %s %s %s%s", 
          verb, number, candidate, pluralModifier );
     }
   
     private void createPluralDependentMessageParts(int count) {
       if (count == 0) {
         thereAreNoLetters();
       } else if (count == 1) {
         thereIsOneLetter();
       } else {
         thereAreManyLetters(count);
       }
     }”

“private void thereAreManyLetters(int count) {
       number = Integer.toString(count);
       verb = "are";”
       pluralModifier = "s";
     }
   
     private void thereIsOneLetter() {
       number = "1";
       verb = "is";
       pluralModifier = "";
     }
   
     private void thereAreNoLetters() {
       number = "no";
       verb = "are";
       pluralModifier = "s";
     }
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks.

```

###### Don't Add Gratuitous Context

- In an imaginary app called `Gas Station Deluxe` it is a bad idea to prefix every class with `GSD`
- This causes you to work against your tools
- You type `G` for a search and receive a huge list

- Likewise, say you invented a `MailingAddress` class in GSD's accounting module and named it `GSDAccountAddress`
- Later you need a mailing address for your customer contact application
- Do you use `GSDAccountAddress`?
- 10/17 characters are redundant or irrelevant

- Shorter names are generally better than longer ones, as long as they are clear
- Add no more context to the name than is necessary

- The names `accountAddress` and `customerAddress` are fine for instances of the class `Address` but could be poor names for classes
- `Address` is a fine name for a class
- If I need to differentiate btwn MAC addresses, port addresses, Web addresses, consider `PostalAddress`, `MAC`, and `URI`
- Names are more precise which is the point of naming

###### Final Words

- Hardest thing about choosing good names is that it requires good descriptive skills and a shared cultural background
- This is a teaching issue rather than a technical, business, or management issue

- People are afraid of renaming things for fear that some over developers will object
- Usually we end up being grateful when names change (for the better)


#### 3 Functions

- Functions are the first line of organization in any program

###### Small

- First rule of functions is that they should be small
- Second rule of functions is that `they should be smaller than that`

- Functions should be transparently obvious about what they are doing
- Each should lead you to the next in a compelling order

###### Blocks and Indenting

- Blocks within `if`/`else`/`while` statements, and so on should be one line wrong
- Not only does this keep the enclosing function small, but it also adds documentary value b/c the function called within the block can have a nicely descriptive name

- Functions should not be large enough to hold nested structures
- Indent level of a function should not be greater than one or two
- Doing this makes the function easier to understand and read

###### Do One Thing

`Functions should do one thing. They should do it well. They should do it only.`

- Is Listing 3-3 doing one thing?

```
“Listing 3-3 HtmlUtil.java (re-refactored)
   public static String renderPageWith
       SetupsAndTeardowns(
   PageData pageData, boolean isSuite) throws Exception {
     if (isTestPage(pageData))
       includeSetupAndTeardownPages(pageData, isSuite);
     return pageData.getHtml();
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- Easy to make case that it is doing 3 things:
    1. Determining whether the page is a test page
    2. If so, including setups and teardowns
    3. Rendering the page in HTML

- So is it doing 1 thing or 3 things?
- Notice that the 3 steps of the function are one level of abstraction below the stated name of the function
- We can describe the function by describing it as a brief `TO` paragraph

```
TO RenderPageWithSetupsAndTeardowns, we check to see whether the page is a test page and if so, we include the setups and teardowns. In either case we render the page in html
```

- If a function only does those steps that are one below the staed name of the function, then the function is doing one thing
- The reason we write functionsis to decompose a large concept(in other words, the name of the function) into a set of abstractions at the next level of abstraction

- Hardly meaningfully shrink Listing 3-3
- Could extract the `if` statement into a function `includesSetupsAndTeardownsIfTestPage` but that simply restates the code w/o changing the level of abstraction

- Another way to know that a function is doing more than `one thing` is if you can extract another function from it w/ a name that is not merely a restatement of its implementation

######## Sections within Functions

- Look at listing 4-7

“public class GeneratePrimes
   {
     /**
      * @param maxValue is the generation limit.
      */
     public static int[] generatePrimes(int maxValue)
     {
       if (maxValue >= 2) // the only valid case
       {
         // declarations
         int s = maxValue + 1; // size of array
         boolean[] f = new boolean[s];
         int i;
         // initialize array to true.
         for (i = 0; i < s; i++)
           f[i] = true;
   
         // get rid of known non-primes
         f[0] = f[1] = false;
   
         // sieve
         int j;
         for (i = 2; i < Math.sqrt(s) + 1; i++)”
“ {
           if (f[i]) // if i is uncrossed, cross its multiples.
           {
             for (j = 2 * i; j < s; j += i)
               f[j] = false; // multiple is not prime
           }
         }
   
         // how many primes are there?
         int count = 0;
         for (i = 0; i < s; i++)
         {
           if (f[i])
             count++; // bump count.
         }
   
         int[] primes = new int[count];
   
         // move the primes into the result
         for (i = 0, j = 0; i < s; i++)
         {
           if (f[i])  // if prime
             primes[j++] = i;
         }
   
         return primes;  // return the primes
       }
       else // maxValue < 2
         return new int[0]; // return null array if bad input.
     }
   }”

```

- Notice that the `generatePrimes` function dis divided into sections such as `declaration`, `initializations`, and `sieve`
- This is an obvious indicator of doing more than one thing 
- Functions that do one thing cannot be reasonably divided into sections
```

###### One Level of Abstraction per Function

- In order to make sure functions are doing `one thing` we need to make sure that the statements within our functions are all at the same level of abstraction

- Mixing levels of abstraction within a function is always confusing
- Readers may not be able to tell whether a particular expression is an essential concept or detail
- Once details are mixed w/ essential concepts, more and more details tend to accrete (grow by accumulation and coalescence) within functions

######## Reading Code from Top to Bottom: `The Stepdown Rule`

- We want the code to read like a top-down narrative
- We want every function to be followed by those at the next level of abstraction so we can read the program, descending one level of abstraction at a time as we read down the list of functions
- Called `The Stepdown Rule`

- To say this differently, we want to be able to read the program as though it were a set of `TO` paragraphs, each of which is describing the current level of abstraction and referencing subsequent `TO` paragraphs at the next level down

```
To include the setups and teardowns, we include setups, then we include the test page content, and then we include the teardowns

To include the setups, we include the suite setup if this is a suite, then we include the regular setup

To include the suite setup, we search the parent hierarchy for the `SuiteSetUp` page and add an include statement with the path of that page

To search for the parent...
```

- It is very difficult for programmers to learn to follow this rule and write functions that stay at a single level of abstraction
- But learning to do this is very important
- It is the key to keeping functions short and making sure they do `one thing`
- Making code read like a top-down set of `TO` paragraphs is an effective technique for keeping the abstraction level consistent

- Listing 3-7 shows the `testableHtml` function refactored using these principles
- Each function introduces the next, and each function remains at a consistent level of abstraction 

```
“SetupTeardownIncluder
Listing 3-7 SetupTeardownIncluder.java
   package fitnesse.html;
”
“import fitnesse.responders.run.SuiteResponder;
   import fitnesse.wiki.*;
   
   public class SetupTeardownIncluder {
     private PageData pageData;
     private boolean isSuite;
     private WikiPage testPage;
     private StringBuffer newPageContent;
     private PageCrawler pageCrawler;
   
   
     public static String render(PageData pageData) throws Exception {
       return render(pageData, false);
     }
   
     public static String render(PageData pageData, boolean isSuite)
       throws Exception {
       return new SetupTeardownIncluder(pageData).render(isSuite);
     }
   
     private SetupTeardownIncluder(PageData pageData) {
       this.pageData = pageData;
       testPage = pageData.getWikiPage();
       pageCrawler = testPage.getPageCrawler();
       newPageContent = new StringBuffer();
     }”
“private String render(boolean isSuite) throws Exception {
        this.isSuite = isSuite;
       if (isTestPage())
         includeSetupAndTeardownPages();
       return pageData.getHtml();
     }
   
     private boolean isTestPage() throws Exception {
       return pageData.hasAttribute("Test");
     }
   
     private void includeSetupAndTeardownPages() throws Exception {
       includeSetupPages();
       includePageContent();
       includeTeardownPages();
       updatePageContent();
     }
   

     private void includeSetupPages() throws Exception {
       if (isSuite)
         includeSuiteSetupPage();
       includeSetupPage();
     }
   
     private void includeSuiteSetupPage() throws Exception {
       include(SuiteResponder.SUITE_SETUP_NAME, "-setup");
     }”
“private void includeSetupPage() throws Exception {
       include("SetUp", "-setup");
     }
   
     private void includePageContent() throws Exception {
       newPageContent.append(pageData.getContent());
     }
   
     private void includeTeardownPages() throws Exception {
       includeTeardownPage();
       if (isSuite)
         includeSuiteTeardownPage();
     }
   
     private void includeTeardownPage() throws Exception {
       include("TearDown", "-teardown");
     }
   
     private void includeSuiteTeardownPage() throws Exception {
       include(SuiteResponder.SUITE_TEARDOWN_NAME, "-teardown");
     }
   
     private void updatePageContent() throws Exception {
       pageData.setContent(newPageContent.toString());
     }
   
     private void include(String pageName, String arg) throws Exception {
       WikiPage inheritedPage = findInheritedPage(pageName);”

“ if (inheritedPage != null) {
         String pagePathName = getPathNameForPage(inheritedPage);
         buildIncludeDirective(pagePathName, arg);
       }
     }
   
     private WikiPage findInheritedPage(String pageName) throws Exception {
       return PageCrawlerImpl.getInheritedPage(pageName, testPage);
     }
   
     private String getPathNameForPage(WikiPage page) throws Exception {
       WikiPagePath pagePath = pageCrawler.getFullPath(page);
       return PathParser.render(pagePath);
     }
   
     private void buildIncludeDirective(String pagePathName, String arg) {
       newPageContent
         .append("\n!include ")
         .append(arg)
         .append(" .")
         .append(pagePathName)
         .append("\n");
     }
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks.

```