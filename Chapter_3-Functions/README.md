###### Small!

- First rule of functions it that they should be small
- Second rule of functions is that `they should be smaller than that`
- Listing 3-2 should be refactored into Listing 3-3

Listing 3-2

```
public static String renderPageWithSetupsandTeeardowns(pageData pageData, boolean isSuite) throws Exception {
    boolean isTestPage = pageData.hasAttribute("Test");
    if (isTestPage) {
        Wikipage testPage = pageData.getWikiPage();
        StringBuffer newPageContent = new StringBuffer();
        includeSetupPages(testPage, newPageContent, isSuite);
        newPageContent.append(pageData.getContent());
        includeTeardownPages(testPage, newPageContent, isSuite);
        pageData.setContent(newPageContent.toString());
    }
    return pageData.getHtml();
}
```

Listing 3-3

```
public static String renderPageWithSetupsandTeardowns(pageData pageData, boolean isSuite) throws Exception {
    boolean isTestPage = pageData.hasAttribute("Test");
    if (isTestPage(pageData))
        includeSetupandTeardownPages(pageData, isSuite);
    return pageData.getHtml();
}
```

######## Blocks and Indenting

- This implies that the blocks within `if` statements, `else` statements, `while` statements`, and so on should be only one line long
- Probably that line should be a function call
- Not only does this keep the enclosing function small, but it also adds documentary value b/c the funciton called within the block can have a nicely descriptive name

- This also implies that functions should not be large enough to hold nested structures
- Therefore, the indent level of a function should not be greater than one or two
- This makes the functions easier to read and understand

###### Do One Thing

- `Functions should do only one thing. They should do it well. They should it only`

- Does Listing 3-3 do one thing?
- Easy to make the case that it's doing 3 things

1. Determine whether the page is a test page
2. If so, include setups and teardowns
3. Rendering the page in HTML

- Is the function doing 1 or 3 things?
- Notice that the 3 steps of the function are one level of abstraction below the stated name of the function
- We can describe a function by describing it as a brief `TO` paragraph

```
TO RenderPageWithSetupsAndTeardowns, we check to see whether the page is a test page and if so, we include the setups and teardowns. In either case we render the page in HTML
```

- If a function does only those steps that are one level below the stated name of the function, then the function is doing one hting
- The reason we write functions is to decompose a larger concept into a set of steps at the next level of abstraction

- Listing 3-2 has two levels of abstraction as proved by our ability to shrink it down
- But it would be very hard to meaningfully Listing 3-3
- We could extract the `if` statement into a function named `includeSetupsAndTearDownsIfTestPage` but that simply restates the code without changing the level of abstraction

- `Another way to know that a function is doing more than "one thing" is if you can extract another function form it with a name that is not merely a restatement of its implementation`

######## Sections within Functions

- Listing 4-7
- Notice that the `generatePrimes` function is divided into sections such as `declarations`, `initializations`, and `sieve`
- This is an obvious symptom of doing more than one thing
- Functions that do one thing cannot be reasonably divided into sections

```
public static int[] generatePrimes(int maxValue) {

    if (maxValue >= 2)
    {
        // declarations
        int s = maxValue + 1; //size of array
        boolean[] f = new boolean[s];
        int i;

        //initialize array to true
        for (i = 0; i < s; i++)
            f[i] = true;
        
        // get rid of known non-primes
        f[0] = f[1] = false;

        //sieve
        int j;
        for (i = 2; i < Math.sqrt(s) + 1; i++) {
            if (f[i]) // if i is uncrossed, cross its multiples
            {
                for (j = 2 * i; j < s; j += i)
                    f[j] = false; //multiple is not prime
            }
        }

        // how many primes are there?
        int count = 0;
        for (i = 0; i < s; i++)
        {
            if (f[i]) //if prime
                primes[j++] = i;
        }
    }
        return prime;
        else // maxValue < 2
            return new int[0]; // return null array if bad input
}
```

###### One Level of Abstraction per Function

- In order to make sure our functions are doing `one thing`, we need to make sure that the statements within our functions are all at the same level of abstraction
- Easy to see how Listing 3-1 violates the rule
- There are concepts that are at a very high level of abstraction, such as `getHtml()`
- Others that are at an intermediate level of abstraction, such as `String pagePathName = PathParser.render(pagePath)`
- Still others are at a low level such as `.append("\n")`

- Mixing levels of abstration within a function is confusing
- Readers may not be able to tell whether a particular expression is an essential concept or a detail
- Worse, once details are mixed with essential concepts, more and more details tend to grow within the function

######## Reading Code from Top to Bottom: The Stepdown Rule

- Want code to read like a top-down narrative
- Want every function to be followed by those at the next level of abstraction so that we can read the program, descending one level of abstraction at a time as we read down the list of functions
- `The Stepdown Rule`

- Want to be able to read the program as though it were a `TO` paragraphs, each of which is describing the current level of abstraction and referencing subsequent `TO` paragraphs at the next level down

```
To include the setups and tears, we include setups, then we include the test page content, and we include teardowns.
    To include the setups, we include the suite setup if this is a suite, then we include the regular setup.
        To include the suite setup, we search the parent hierarchy for the "SuiteSetUp" page and add an include statement with the path of that page.
            To search the parent...
```

- It is hard to follow this rule and write functions that stay at a single level of abstraction
- But learnign this is very important
- It is the key to keeping functions short and making sure they do `one thing`
- Making the code read like a top-down set of `TO` paragraphs is an effective technique for keeping the abstraction level consistent

- Listing 3-7 shows this principle

```
public class SetupTeardownInclude {

    private PageDate pageData;
    private boolean isSuite;
    private WikiPage testPage;
    private StringBuffer newPageContent;
    private PageCrawler pageCrawler;

    public static String render(PageData pageData) throws Exception {
        return render(pageData, false);
    }

    public static String render(PageData pageData, boolean isSuite) throws Exception {
        return new SetupTeardownIncluder(pageData).render(isSuite);
    }

    private SetupTeardownIncluder(PageData pageData) {
        this.pageData = pageData;
        testPage = pageData.getWikiPage();
        pageCrawler = testPage.getPageCrawler();
        newPageContent = new StringBuffer();
    }

    private String render(boolean isSuite) throws Exception {
        this.isSuite = isSuite();
        if (isTestPage())
            includeSetupAndTeardownPages();
        return pageData.getHtml();
    }

    private boolean isTestPage() throws Exception {
        return pageData.hasAttribute("Test");
    }

    private void includeSetupandTeardownPages() throws Exception {
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
        include (SuiteResponder.SUITE_SETUP_NAME, "-setup");
    }

    private void includeSetupPage() thows Exception {
        include ("SetUp", "-setup");   
    }

    private void includePageContent() throws Exception {
        newPageContent.append(pageData.getContent());
    }

    private void includeTeardownPages() throws Exception {
        includeTeardownPage();
        if (isSuite)
            includeSuiteTeardownPage();
    }

    private void includeSuiteTeardownPage() throws Exception {
        include(SuiteResponder.SUITE_TEARDOWN_NAME, "-teardown");
    }

    private void updatePageContent() throws Exception {
        pageData.setContent(newPageContent.toString());
    }

    private void include (String pageName, String arg) throws Exception {
        WikiPage inheritedPage = findInheritedPage(pageName);
        if (inheritedPage != null) {
            String pagePathName = getPathNameForPage(inheritedPage);
            buildIncludeDirective(pagePathName, arg);
        }
    }

    prviate WikiPage findInheritedPage(String pageName) throws Exception) {
        return PageCrawlerImpl.getInheritedPage(pageName, testPage);
    }

    private String getPathNameForPage (WikiPage page) throws Exception {
        WikiPagePath pagePath = pageCrawler.getFullPath(path);
        return PathParser.render(pagePath);
    }

    private void buildIncludeDirective(String pagePathName, String arg) {
        newPageContent
            .append("\n!include ")
            .append(arg)
            .append(pagePathName)
            .append("\n")
    }

}
```

###### Switch Statements

- Hard to make a small switch statement
- Even a `switch` statement w/ only two cases is larger than a single block or function to be
- Hard to make a `switch` statement that does one thing
- `Switch` statements always do `N` things
- Can't always avoid `switch` statements
- We can make sure that each `switch` statements is buried in a low-level class and is never repeated

```
public Money calculatePay(Employee e) 
   throws InvalidEmployeeType {
       switch (e.type) {
         case COMMISSIONED:
           return calculateCommissionedPay(e);
         case HOURLY:
           return calculateHourlyPay(e);
         case SALARIED:
           return calculateSalariedPay(e);
         default:
           throw new InvalidEmployeeType(e.type);
       }
     }

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks.
```


- Several problems w/ this function
- First, it's large, and when new employee types are added, it will grow
- Clearly does more than one thing
- Violates the Single Responsibility Principle (SRP) b/c there is more than one reason to change
- Fourth, it volates the Open Closed Principle (OCP) b/c it must change whenever new types are added
- Possibly the worst problem w/ this function is that there are an unlimited number of other functions that will have the same structure
- Solution to this problem (Listing 3-5) is to bury the `switch` statements in the basement of an `Abstract Factory`, and never let anyone see it

- The factory will use the `switch` statement to create appropriate instances of the derivatives of `Employee`, and the various functions, such as `calculatePay`, `isPayday`, and `deliverPay`, will be dispatched polymorphically through the `Employee` interface

- General rule for `switch` statement is that they can be tolerated if they appear only once, are used to create polymorphic objects, and are hidden behind an inheritance relationship so that the rest of the system can't see them

```
public abstract class Employee {
     public abstract boolean isPayday();
     public abstract Money calculatePay();
     public abstract void deliverPay(Money pay);
   }
   -----------------
   public interface EmployeeFactory {
     public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType;
   }
   -----------------
   public class EmployeeFactoryImpl implements
          EmployeeFactory {
     public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType {
       switch (r.type) {
         case COMMISSIONED:
           return new CommissionedEmployee(r) ;
         case HOURLY:
           return new HourlyEmployee(r);
         case SALARIED:
           return new SalariedEmploye(r);
         default:
           throw new InvalidEmployeeType(r.type);
       }
     }
   }
```

###### Use Descriptive Names

- In Listing 3-7 changed name of our example function from `testableHtml` to `setupTeardownIncluder.render`
- Ward's principle: `You know you are working on clean code when each routine turns out to be pretty much what you expected`
- Half the battle to achieving that principle is choosing good names for small functions to do one thing
- The smaller and more focused a function is, the easier it is to choose descriptive name

- Don't be afraid to make a name long
- A long descriptive name is better than a short enigmatic name
- A long descriptive name is better than a long descriptive comment
- Use a naming convention that allows multiple words to be easily read in the function names, and then make use of those multiple words to give the function a name that says what it does

- Don't be afraid to spend time choosing a name
- Try several different names and read the code with each in plae

- Choosing descriptive names will clarify the design of the module in your mind and help you to improve it
- it is not uncommon that hunting for a good name results in a favorable restructuring of your code

- Be consistent in your names
- Use the same phrases, nouns, and verbs in the function names you choose for your modules
- Consider the names `includeSetupAndTeardownPages`, `includeSetupPages`, `includeSuitesetupPage`, and `includeSetuPage`
- The similar phraseology in those names allows the sequence to tell a story

###### Function Arguments

- Ideal number of arguments for a function is zero (niladic)
- Next comes on (monadic)
- Followed closely by two (dyadic)
- Three arguments (triadic) should be avoided where possible
- More than 3 (polyadic) requires very special justification

- Arguments are hard
- They take a lot of conceptual power
- `includeSetupPage()` is easier to understand than `includeSetupPageInto(newPageContent)`

- Argumentss are even harder form a testing point of view
- If there are no arguments, this is trivial
- With many arguments, testing every combination of appropriate values can be daunting

- Output arguments are harder to understand than input arguments
- When we read a function, we are used to the idea of information going `in` to the function through arguments and `out` through the return value
- Don't usually expect info to be going out through the arguments
- So output arguments often cause us to do a double-take

######## Common Monadic Forms

- Two common reasons to pass a single argument into a function
- You may be asking a question about that argument, as in `boolean fileExists("MyFile")`
- Or you may be operating on that argument, transforming it into somethign else and `returning it`
- For example, `InputStream fileOpen("MyFile")` transforms a file named `String` into an `InputStream` return value
- These two uses are what readers expect when they see a function
- You should choose names that make distinction clear, and always use the two forms in a consistent context

- Somewhat less common, but still very useful form for a single argument function, is an `event`
- In this form there is an input argument but no output argument
- The overall program is meant to interpret the function call as an event and use the argument to alter the state of the system, for example, `void passwordAttemptFailedNTimes(int attempts)`
- Use this with care
- Should be clear to the reader that this is an event
- Choose names and contexts carefully

- Try to avoid monadic functions that don't follow these forms, like, `void includeSetupPageInto(StringBuffer pageText)`
- Using an output argument instead of a return value for a transformation is confusing
- If a function is going to transform its input argument, the transformation should appear as a return value

######## Flag Arguments

- Flag arguments are ugly
- Passing a boolean into a function is a bad practice
- It immediately complicates the signature of the method, proclaiming that this function does more than one thing
- It does one thing if the flag is true and another if the flag is false

######## Dyadic Functions

- A function with two arguments is harder to understand than a monadic function
- For example, `writeField(name)` is easier to understand than `writeField(outputStream, name)`
- Second requires a short pause until we learn to ignore the first parameter
- And `that` eventually results in problems b/c we should never ignore any part of code
- The parts we ignore are where the bugs will hide

- Times when 2 arguments is appropriate
- `Point p = new Point(0, 0)`
- The two arguments in this case `are ordered components of a single value`

######## Argument Objects

- When a function seems to need more than two or three arguments, it is likely that some of those arguments ought to be wrapped into a class of their own
- Consider for example the different between the two following declarations

```
Circle makeCircle(double x, double y, double radius);
Circle makeCircle(Point center, double radius);
```

- Reducing the number of arguments by creating object out of them may seem like cheating but it's not
- When groups of variables are passed together, the way `x` and `y` are in the example above, they are likely part of a concept that deserves a name of its own

######## Argument Lists

- Sometimes we want to pass a variable number of arguments into a function
- Consider the `String.format` methoid

`String.format("%s worked %.2f hours.", name, hours)

- If the variable argumetns are all treated identically, then they are equivalent to a singel argument of type `List`
- By that reasoning, `String.format` is actualy dyadic

`public String format(String format, Object... args)`

- Functions that take variable arguments can be monads, dyads, or even triads

```
void monad(Integer ...args);
void dyad(String name, Integer... args);
void triad(String name, int count, Integer... args)
```

######## Verbs and Keywords

- Choosing good names for functions can go a long way toward explaining the intent of the function and the order and the intent of the arguments
- In the case of a monad, the function and argument should form a very nice `verb/noun` pair
- For example, `write(name)`is a good combination
- Whater this `name` thing is, it is being `written`
- An even better name might be `writeField(name)` which tells us that the `name` thing is a `field`

- `assertEqual` might be better written as `assertExpectedEqualsActual(expected, actual)`
- This mitigates the problem of having to remember the ordering of the arguments

###### Have No Side Effects

- Your promises should only do one thing
- But it ends up doing other `hidden` things
- Sometimes it will make unexpected changes to the variables of its own class
- Sometimes it will make unexpected changes to the parameters passed into thte function or to the system globals
- In either case, they are bad and can often result in strange couplings and other dependencies

- Consider Listing 3-6 below
- This function uses a standard algorithm to match a `userName` to a `password`
- It returns `true` if they match and `false` if anything goes wrong
- It also has a side effect

```
public class UserValidator {
    private Cryptographer cryptographer;
    public boolean checkPassword(String userName, String password) {
        User user = UserGateway.findByName(userName);
        if (user != USER.NULL) {
            String codedPhrase = user.getPhraseEncodedByPassword();
            String phrase = cryptographer.decrypt(codedPhrase, password);
            if ("Valid Password".equals(phrase)) {
                Session.initialize();
                return true;
            }
        }
        return false;
    }
}
```

- The side effect is to the call `System.initialize()`
- The `checkPassword` function by its name says that it checks the password
- The name does not imply that it initializes the session
- `So a caller who believes what the name of the function says runs the risk of erasing the existing session data when he/she decides to check the validity of the user`

- This side effect creates a `temporal coupling`
- `checkPassword` can only be called at certain times (in other words, when it is safe to initialize the session)
- If it is called out of order, session data may inadvertently be lost
- Temporal couplings are confusing, especially when hidden as a side effect
- In this case we might rename the function to `checkPasswordAndInitializeSession`, but it certainly violates `Do one thing`

######## Output Arguments

- Arguments are most naturally interpreted as `inputs` to functions

`appendFooter(s)`

- Does this function append `s` as the footer to something?
- Does it append some footer to `s`?
- Is `s` an input or an output?

- It doesn't take long to look at the function signature and see:

`public void appendFooter(StringBuffer report)`

- This clarifies the issue but at the expense of checking the declaration of the function
- Anything that forces you to check the function signature is equivalent to a `double-take`
- It's a cognitive break and should be avoided

- Much of the need for output arguments disappears in OO languages because `this` is intended to act as an output argument
- In other words it would be better for `appendFooter` to be invoked as `report.appendFooter`

- In general output arguments should be avoided
- If your function must change the state of something, have it change the state of its owning object

###### Command Query Separation

- Functions should do something or answer something, not both
- Either your functions should change the state of an object, or it should reutrn some info about that object
- Doing both leads to confusion
- Consider the following

`public boolean set(String attribute, String value)`

- This function sets the value of a named attribute and returns true if it is successful or false if no such attribute exists
- This leads to odd statements like

`if (set("username", "unclebob"))...`

- Imagine this from the point of view of the reader
- What does it mean?
- Is it asking whether the `username` attribute was previously set to `unclebob`?
- Or is it asking wether the `username` attribute was successfully set to `unclebob`?
- It's hard to infer the meaning from the call b/c it's not clear whether the word `set` is a verb or an adjective

- Author intended `set` to be a verb, but in the context of the `if` statement it feels like an adjective
- So the statement reads as `If the username attribute was previously set to unclebob" and not "set the username attribute to unclebob and if that worked then..."
- Could try to resolve this by renaming the `set` function to `setAndCheckIfExists` but that doesn't much help the readability of the `if` statement
- Real solution is to separate the command from the query so that the ambiguity cannot occur

```
if (attributeExists("username")) {
    setAttribute("name", "unclebob")
}
```

###### Prefer Exceptions to Returning Error Codes

- Returning error codes from command functions is a subtle violation of a command query separation
- It promotes commands being used as expressions in the predicates of `if` statements

`if (deletePage(page) == E_OK)`

- This does not suffer from verb/adjbective confusion but does lead to deeply nested structures
- When you return an error code, you create the problem that the caller must deal with the error immediately

```
if (deletePage(page) == E_OK) {
    if (registry.deleteReference(page.name) == E_OK) {
        if (configKeys.deleteKey(page.name.makeKey()) == E_OK) {
            logger.log("page deleted");
        } else {
            logger.log("configKey not deleted");
        }
    } else {
        logger.log("deleteReference from registry failed")'
    }
} else {
    logger.log("delete failed");
    return E_ERROR;
}
```

- On the other hand if you use exceptions instead of returned error codes, then the error processing code can be separated from the happy path code and can be simplified

```
try {
    deletePage(page);
    registry.deleteReference(page.name);
    configKeys.deleteKey(page.name.nameKey());
}
catch (Exception e) {
    logger.log(e.getMessage());
}
```

######## Extract Try/Catch Blocks

- `Try/Catch` blocks are ugly
- They confuse the structure of the code and mix error processing with normal processing
- So it is better to extract the bodies of the `try` and `catch` blocks into functions of their own

```
public void delete(Page page) {
    try {
        deletePageAndAllReferences(page);
    }
    catch (Exception e) {
        logError(e);
    }
}

private void deletePageAndAllReferences(Page page) throws Exception {
    deletePage(page);
    registry.deleteReference(page.name);
    configKeys.deleteKey(page.name.makeKey());
}

private void logError(Exception e) {
    logger.log(e.getMessage());
}
```

######## Error Handling Is One thing

- Functions should do one thing
- Error handling is one thing
- Thus a function that handles errors should do nothing else
- This implies that if the keyword `try` exists in a function, it should be the very first word in the function and that there should be nothing after the `catch/finally` blocks

######## The Error.java Dependency Magnet

- Returning error codes usually implies that there is some class or enum in which all the error codes are defined

```
public enum Error {
    OK,
    INVALID,
    NO_SUCH,
    LOCKED,
    OUT_OF_RESOURCES,
    WAITING_FOR_EVENT
}
```

- Classes like this are `dependency magnet`; many other classes must import and use them
- Thus when the `Error enum` changes, all those other classes need to be changed as well
- This puts negative pressure on the `Error` class
- Programmers don't want to add new errors b/c then they have to rebuild and redeploy everything
- So they reuse old error codes instead of adding new ones

- When you use exceptions rather than error codes, then new exceptions are `derivatives` of the exception class
- They can be added without forced changes in other places

###### Don't Repeat Yourself

- Look back Listing 3-1 carefully and you will notice there is an algorithm that gets repeated 4 times, once for each of the `SetUp`, `SuiteSetUp`, `TearDown`, and `SuiteTearDown`
- Not easy to spot this duplication b/c the 4 instances are intermixed with other code and aren't formally duplicated
- Still the duplication is a problem b/c it bloats the code and will require four-fold modification should the algorithm ever have to change
- It is also a four-fold opportunity for an error

- Duplication was remedied by the `include` method in Listing 3-7
- Readability of the whole module is enhanced by the reduction of that duplication

- Duplication creates a lot of issues within software
- Many principles and practices have been created for the purpose of controlling or eliminating it


###### How Do You Write Functions Like This?

- Writing software is like any other kind of writing
- When you write a paper or an article, you get your thoughts down first, then ou massage it until it reads well
- First draft might be clumsy and disorganized, so you restructure it and refine it unitl it reads the way you want it to read

- Many times, when you first write functions they come out long and complicated
- They have lots of indenting and nested loops
- They have long argument lists
- The names are arbitrary, and there is duplicated code
- Should also have a suite of unit tests that cover every one of those lines of code

- Then you refine that code, splitting our functions, changing names, eliminating duplication
- Shrink methods and reorder them
- Sometimes break out whole classes, all while keeping the unit tests passing

- You don't have to write functions the way defined in this chapter to start

###### Conclusion

- Every system is built from domain specific language designed by programemr to describe that system
- Functions are the verbs of that language
- Classes are the nouns
- The art of programming is the art of language design

- Master programmers think of systems as stories to be told rather than programs to be written
- They use the facilities of their chosen programming languages to construct a much richer and more expressive langugae that can be used to tell that story
- Part of that domain-specific langugae is the hierarchy of functions that describe all the actions that take place within the system
- Those actions are written to use the very domain-specific language they define to tell their own part of the story
