###### Class Organization

- Class should begin w/ a list of variables
- Public static constants should come first
- Private static variables second
- Private instance variables 3rd
- Seldom a good reason to have a public variable

- Public functions should follow the list of variables
- Like to put the private utilities called by a pulic function right after the public function iself
- This follows the stepdown rule and helps the program read like a newspaper article

######## Encapsulation

- Like to keep our variables and utility functions private, but we're not fanatic about it
- Sometimes we need to make a variable or a utility function protected so that it can be accessed by a test
- Tests rule
- If a test in the same package needs to call a function or access a variable, we'll make it protected or package scope
- Loosening encapsulation is always a last resort

###### Classes Should be Small!

- Smaller is the primary rule when it comes to designing classes
- How small?

- We count `responsibilities` to determine size of a class

- Listing 10-1 outlines a class, `SuperDashboard` that exposes over 70 public methods
- Some devs might refer to this as a `God Class`

```
“Listing 10-1 Too Many Responsibilities
   public class SuperDashboard extends JFrame implements MetaDataUser
      public String getCustomizerLanguagePath()
      public void setSystemConfigPath(String systemConfigPath)
      public String getSystemConfigDocument()
      public void setSystemConfigDocument(String systemConfigDocument)
      public boolean getGuruState()
      public boolean getNoviceState()
      public boolean getOpenSourceState()
      public void showObject(MetaObject object)
      public void showProgress(String s)
      public boolean isMetadataDirty()
      public void setIsMetadataDirty(boolean isMetadataDirty)
      public Component getLastFocusedComponent()
      public void setLastFocused(Component lastFocused)
      public void setMouseSelectState(boolean isMouseSelected)
      public boolean isMouseSelected()
      public LanguageManager getLanguageManager()
      public Project getProject()
      public Project getFirstProject()
      public Project getLastProject()”
      public String getNewProjectName()
      public void setComponentSizes(Dimension dim)
      public String getCurrentDir()
      public void setCurrentDir(String newDir)
      public void updateStatus(int dotPos, int markPos)
      public Class[] getDataBaseClasses()
      public MetadataFeeder getMetadataFeeder()
      public void addProject(Project project)
      public boolean setCurrentProject(Project project)
      public boolean removeProject(Project project)
      public MetaProjectHeader getProgramMetadata()
      public void resetDashboard() 
      public Project loadProject(String fileName, String projectName)
      public void setCanSaveMetadata(boolean canSave)
      public MetaObject getSelectedObject()
      public void deselectObjects()
      public void setProject(Project project)
      public void editorAction(String actionName, ActionEvent event)
      public void setMode(int mode)
      public FileManager getFileManager()
      public void setFileManager(FileManager fileManager)
      public ConfigManager getConfigManager()
      public void setConfigManager(ConfigManager configManager)
      public ClassLoader getClassLoader()
      public void setClassLoader(ClassLoader classLoader)
      public Properties getProps()
      public String getUserHome()
      public String getBaseDir()
      public int getMajorVersionNumber()
      public int getMinorVersionNumber()
      public int getBuildNumber()
      public MetaObject pasting(
         MetaObject target, MetaObject pasted, MetaProject project)
      public void processMenuItems(MetaObject metaObject)
      public void processMenuSeparators(MetaObject metaObject)
      public void processTabPages(MetaObject metaObject)
      public void processPlacement(MetaObject object)
      public void processCreateLayout(MetaObject object)
      public void updateDisplayLayer(MetaObject object, int layerIndex)
      public void propertyEditedRepaint(MetaObject object)
      public void processDeleteObject(MetaObject object)
      public boolean getAttachedToDesigner()
      public void processProjectChangedState(boolean hasProjectChanged)
      public void processObjectNameChanged(MetaObject object)
      public void runProject() 
      public void setAçowDragging(boolean allowDragging)
      public boolean allowDragging()
      public boolean isCustomizing()
      public void setTitle(String title)
      public IdeMenuBar getIdeMenuBar()
      public void showHelper(MetaObject metaObject, String propertyName)
      // … many non-public methods follow …
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- But what if `SuperDashboard` contained only the methods shown in Listing 10-2

```
“public class SuperDashboard extends JFrame implements MetaDataUser
       public Component getLastFocusedComponent()
       public void setLastFocused(Component lastFocused)
       public int getMajorVersionNumber()
       public int getMinorVersionNumber()
       public int getBuildNumber()
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- Only 5 methods
- Still too many b/c it still has too many `responsibilities`

- The name of a class should describe what responsibilities it fulfills
- In fact, naming is probably the first way of helping determine class size
- If precise name cannot be derived, then it's likely too large
- The more ambiguous the class name, the more likely it has too many responsibilities
- For example, class names inclusing weasel words like `Processor` or `Manager` or `Super` often hint at unfortunate aggregation of responsibilities

- We should be able to write a brief description of the class in about 25 words, w/o using the words `if`, `and`, `or`, or `but`
- How would we describe `SuperDashboard`?
    * The `SuperDashboard` provides access to the component that last held focus, and allows us to tract the version and build numbers
    * The first `and` is a hint that `SuperDashboard` has too many responsibilities

######## The Single Responsibility Principle

- `SRP states that a class or module should have one, and only one, reason to change`
- This principle gives us both a definition of responsbility and a guidelines for class size
- Classes should have one responsibility - one reason to change

- The seemingly small `SuperDashboard` class in Listing 10-2 has two reasons to change
    * First it tracks version information that would seemingly need to be updated every time the software gets shipped
    * Second it manages Java Swing components
- No doubt we'll want to update the version number if we change any of the Swing code, but the converse isn't necessarily true: We might want the version info based on changed to other code in the system

- Trying to identify responsibilities (reasons to change) often helps us recognize and create better abstractions in our code
- Can easily extract all three `SuperDashboard` methods that deal with version information into a separate clas called `Version` (listing 10-3)
- The `Version` class is a construct that has a high potential for reuse in other apps

```
“Listing 10-3 A single-responsibility class
   public class Version {
       public int getMajorVersionNumber()
       public int getMinorVersionNumber()
       public int getBuildNumber()
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- SRP is one of the more important concepts in OO design
- Also one of the simper concents to understand and adhere to
- SRP is also often the most abused class design principle
- We regularly encounter classes that do far too many things
- Why?

- Getting software to work and making software clean are different activities
- Most of us have limited room in our heads, so we focus on getting our code to work more than organization and cleanliness
- This is appropriate
- Maintaining a separation of concerns is just as important in our programming activities as it is in our programs

- The problem is tha too many of us think that we are done once the program works
- Fail to switch to the `other` concern of organization of cleanliness
- We move on to the next problem rather than going back and breaking the overstuffed classes into decoupled units w/ single responsibilities

- At the same time, many devs fear that a large number of small, single-purpose classes make it difficult to understand the bigger picture
- Concerned that they must navigate from class to class in order to figure out how a large piece of work is accomplished

- However, a system w/ many small classes has no more moving parts than a system w/ a few large classes
- There isjust as much to learn in a system w/ a few large classes
- The question is: Do you want your tools organized into toolboxes w/ many small drawers each containing well-defined and well-labeled components? Or do you want a few drawers that you toss everything into?

- Every sizable system will contain a large amount of logic and complexity
- Primary goal in managing such complexity is to `organize` it so that a developer knows where to look to find things and need only understand the directly affected complexity at any given type
- In contrast, a system w/ larger, multipurpose classes always hampers us by insisting we wade through lots of things that we don't need to know right now

- `We want our system to be composed of many small classes, not a few large ones`
- Each small class encapsulates a single responsibility, has a single reason to change, and collaborates w/ a few others to achieve the desired system behaviors

###### Cohesion

- Classes should have a small number of instance variables
- Each of the methods of a class should manipulate one or more of those variables
- In general the more variables a method manipulates the more cohesive that method is to its class
- A class which each variable is used by each method is maximally cohesive

- In general it is neither advisable nor possible to create such maximally cohesive classes; on the other hand, we would like cohesion to be high
- When cohesion is high, it means that we the methods and variables of the classes are co-dependent and hang together as a logical whole

- Consider the implementation of a `Stack` in Listing 10-4
- This is a very cohesive class
- Of the 3 methods only `size()` fails to use both variables

```
“Listing 10-4 Stack.java A cohesive class.
   public class Stack {
     private int topOfStack = 0;
     List<Integer> elements = new LinkedList<Integer>();

     public int size() {
       return topOfStack;
     }

     public void push(int element) {
       topOfStack++;
       elements.add(element);
     }

     public int pop() throws PoppedWhenEmpty {
       if (topOfStack == 0)
         throw new PoppedWhenEmpty();
       int element = elements.get(--topOfStack);
       elements.remove(topOfStack);
       return element;
    }
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- The strategy of keeping functions small and keeping parameter lists short can sometimes lead to a proliferation of instance variables that are used by a subset of methods
- When this happens, it is almost always means that there is at least one other class trying to get out of the larger class
- You should try to separate the variables and methods into two or more classes such that the new classes are cohesive

######## Maintaining Cohesion Results in Many Small Classes

- The act of breaking large functions into smaller functions causes a proliferation (rapid increase) of classes
- Consider a function with many variables declared within it
- Let's say you want to extract one small part of that function into a separate function
- However, the code you wan to reuse uses 4 of the variables declared in the function?
- Must you pass all 4 of those variables into the new functions as arguments?

- `Not at all`
- If we promoted those 4 variables to instance variables of the class to instance variables of the class, then we could extract the code w/o passing any variables at all
- It would be easy to break the function into small pieces

- So breaking a large function into smaller functions often gives us the opportunity to split several smaller classes out as well
- This gives our program a much better organization and more transparent structure

- As a demonstration, let's use an example from Knuth's `Literate Programming`
- Listing 10-5 shws a translation into Java of Knuth's PrintPrimes program
- Good starting place for breaking up big functions into many small functions and classes

```
“Listing 10-5 PrintPrimes.java
   package literatePrimes;
   
   public class PrintPrimes {
     public static void main(String[] args) {
       final int M = 1000;
       final int RR = 50;
       final int CC = 4;
       final int WW = 10;
       final int ORDMAX = 30;
       int P[] = new int[M + 1];
       int PAGENUMBER;
       int PAGEOFFSET;
       int ROWOFFSET;
       int C;

       int J;
       int K;
       boolean JPRIME;
       int ORD;
       int SQUARE;
       int N;
       int MULT[] = new int[ORDMAX + 1];

       J = 1;
       K = 1;
       P[1] = 2;
       ORD = 2;
       SQUARE = 9;”
       while (K < M) {
         do {
           J = J + 2;
           if (J == SQUARE) {
             ORD = ORD + 1;
             SQUARE = P[ORD] * P[ORD];
             MULT[ORD - 1] = J;
           }
           N = 2;
           JPRIME = true;
           while (N < ORD && JPRIME) {
             while (MULT[N] < J)
               MULT[N] = MULT[N] + P[N] + P[N];
             if (MULT[N] == J)
               JPRIME = false;
             N = N + 1;
          }
        } while (!JPRIME);
        K = K + 1;
        P[K] = J;
     }
     {
        PAGENUMBER = 1;
        PAGEOFFSET = 1;
        while (PAGEOFFSET <= M) {
          System.out.println(”The First ” + M +
                                     ” Prime Numbers --- Page ” + PAGENUMBER);
          System.out.println(””);
          for (ROWOFFSET = PAGEOFFSET; ROWOFFSET < PAGEOFFSET + RR; ROWOFFSET++){
           for (C = 0; C < CC;C++)
             if (ROWOFFSET + C * RR <= M)
               System.out.format(”%10d”, P[ROWOFFSET + C * RR]);
             System.out.println(””);
          }
          System.out.println(”\f”);
          PAGENUMBER = PAGENUMBER + 1;
          PAGEOFFSET = PAGEOFFSET + RR * CC;
      }
     }
    }
   }

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- This program as a single function is a mess
- Deeply indented structure, plethora of odd variables, and a tightly coupled structure

- Listing 10-6 though 10-8 shows the result of spliting the code in Listing 10-5 into smaller classes and functions, and choosing meaningful names for those classes, functions, and variables

Listing 10-6

```
Listing 10-6 PrimePrinter.java (refactored)
   package literatePrimes;

   public class PrimePrinter {
     public static void main(String[] args) {
     final int NUMBER_OF_PRIMES = 1000;
     int[] primes = PrimeGenerator.generate(NUMBER_OF_PRIMES);

     final int ROWS_PER_PAGE = 50;
     final int COLUMNS_PER_PAGE = 4;
     RowColumnPagePrinter tablePrinter =
       new RowColumnPagePrinter(ROWS_PER_PAGE,
                                COLUMNS_PER_PAGE,
                                ”The First ” + NUMBER_OF_PRIMES +
                                        ” Prime Numbers”);
       tablePrinter.print(primes);
     }
}

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks.
```
Listing 10-7

```
Listing 10-7 RowColumnPagePrinter.java
   package literatePrimes;

   import java.io.PrintStream;

   public class RowColumnPagePrinter {
     private int rowsPerPage;
     private int columnsPerPage;
     private int numbersPerPage;
     private String pageHeader;
     private PrintStream printStream;

     public RowColumnPagePrinter(int rowsPerPage,
                                 int columnsPerPage,
                                 String pageHeader) {
       this.rowsPerPage = rowsPerPage;
       this.columnsPerPage = columnsPerPage;
       this.pageHeader = pageHeader;
       numbersPerPage = rowsPerPage * columnsPerPage;
       printStream = System.out;
   }
```

```
   public void print(int data[]) {
     int pageNumber = 1;
     for (int firstIndexOnPage = 0;
          firstIndexOnPage < data.length;
          firstIndexOnPage += numbersPerPage) {
       int lastIndexOnPage =
         Math.min(firstIndexOnPage + numbersPerPage - 1,
                  data.length - 1);
       printPageHeader(pageHeader, pageNumber);
       printPage(firstIndexOnPage, lastIndexOnPage, data);
       printStream.println(”\f”);
       pageNumber++;
     }
   }

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks.
```

```
“private void printPage(int firstIndexOnPage,
                          int lastIndexOnPage,
                          int[] data) {
     int firstIndexOfLastRowOnPage =
       firstIndexOnPage + rowsPerPage - 1;
     for (int firstIndexInRow = firstIndexOnPage;
          firstIndexInRow <= firstIndexOfLastRowOnPage;
          firstIndexInRow++) {
       printRow(firstIndexInRow, lastIndexOnPage, data);
       printStream.println(””);
      }
    }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

```
“private void printRow(int firstIndexInRow,
                          int lastIndexOnPage,
                          int[] data) {
      for (int column = 0; column < columnsPerPage; column++) {
        int index = firstIndexInRow + column * rowsPerPage;
        if (index <= lastIndexOnPage)
         printStream.format(”%10d”, data[index]);
      }
    }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

```
    private void printPageHeader(String pageHeader,
                                int pageNumber) {
      printStream.println(pageHeader + ” --- Page ” + pageNumber);
      printStream.println(””);
    }

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

```
   public void setOutput(PrintStream printStream) {
      this.printStream = printStream;
    }
   }

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

Listing 10-8

```
“ package literatePrimes;

   import java.util.ArrayList;

   public class PrimeGenerator {
     private static int[] primes;
     private static ArrayList<Integer> multiplesOfPrimeFactors;

     protected static int[] generate(int n) {
       primes = new int[n];
       multiplesOfPrimeFactors = new ArrayList<Integer>();
       set2AsFirstPrime();
       checkOddNumbersForSubsequentPrimes();
       return primes;
     }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

```
“ private static void set2AsFirstPrime() {
       primes[0] = 2;
       multiplesOfPrimeFactors.add(2);
     }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

```
“  private static void checkOddNumbersForSubsequentPrimes() {
       int primeIndex = 1;
       for (int candidate = 3;
            primeIndex < primes.length;
            candidate += 2) {
         if (isPrime(candidate))
           primes[primeIndex++] = candidate;
       }
     }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

```
“private static boolean isPrime(int candidate) {
       if (isLeastRelevantMultipleOfNextLargerPrimeFactor(candidate)) {
         multiplesOfPrimeFactors.add(candidate);
         return false;
       }
       return isNotMultipleOfAnyPreviousPrimeFactor(candidate);
     }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

```
   private static boolean
     isLeastRelevantMultipleOfNextLargerPrimeFactor(int candidate) {
       int nextLargerPrimeFactor = primes[multiplesOfPrimeFactors.size()];
       int leastRelevantMultiple = nextLargerPrimeFactor * nextLargerPrimeFactor;”
       return candidate == leastRelevantMultiple;
   }

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

```
“  private static boolean
     isMultipleOfNthPrimeFactor(int candidate, int n) {
      return
        candidate == smallestOddNthMultipleNotLessThanCandidate(candidate, n);
     }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

```
“private static int
     smallestOddNthMultipleNotLessThanCandidate(int candidate, int n) {
       int multiple = multiplesOfPrimeFactors.get(n);
       while (multiple < candidate)
         multiple += 2 * primes[n];
       multiplesOfPrimeFactors.set(n, multiple);
       return multiple;
     }
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- First thing you might notice is that the program got a lot longer
- Went from over a page to nearly 3 pages in length
- Several reasons for this
    * First, the refactored programs uses longer, more descriptive names
    * Second, the refactored program uses functions and class declarations as a way to add commentary to the code
    * Third, we used whitespace and formatting techniques to keep the program readable

- Notice how the program has been split into 3 main responsibilities
- Main program is contained in the `PrimePrinter` class all by itself
    * Its responsibility is to handle the execution environment
    * It will change if the method of invocation changes
    * For ex, if this program were converted to a SOAP service, this is the class that would be affected

- The `RowColumnPagePrinter` knows all about how to format a list of numbers into pages w/ a certain number of rows and columns
- If the formatting of the output needed changing, then this is the class that would be affected

- The `PrimeGenerator` class knows how to generate a list of prime numbers
- Notice that it is not mean to be instantiated as an object
- The class is just a useful scope in which its variables can be declared and kept hidden
- This class will change if the alogrithm for computing prime numbers changes

- Notice that we did `not` start from scratch and write the program over again
- If you look closely at the two different programs, you'll see that they use the same algorithm and mechanics to get their work done

- The change was made by writing a test suite that verified the `precise` behavior of the first program
- Then a myriad of tiny changes were made, one at a time
- After eacch change, the program was executed to ensure that the behaior had not changed
- One tiny step after another, the first program was cleaned up and transformed into the second

###### Organizing for Change

- For most systems, change is continual
- Every change subjects us to risk that the remainder of the system no longer works as intended
- In a clean system we organize our classes so as to reduce the risk of change

- The `SQL` class in Listing 10-9 is used to generate properly SQl strings given appropriate metadata
- It's a work in progress and, as such, doesn't yet support SQL functionality like `update` statements
- When the time comes for the SQL class to support an update statement, we'll have to `open up` this class to make modifications
- The problem with opening a class is that it introduces risk
- Any modificaitons to the class have the potential of breaking other code in this class
- It must be fully retested

```
   public class Sql {   public Sql(String table, Column[] columns)
      public String create()
      public String insert(Object[] fields)
      public String selectAll()
      public String findByKey(String keyColumn, String keyValue)
      public String select(Column column, String pattern)
      public String select(Criteria criteria)
      public String preparedInsert()
      private String columnList(Column[] columns)
      private String valuesList(Object[] fields, final Column[] columns)
      private String selectWithCriteria(String criteria)
      private String placeholderList(Column[] columns)
   }

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- The SQL class must change when we add a new type of statement
- It also much change when we alter the details of a single statement type
    * For example, if we need to modify the select functionality to support subselects
- These two reasons for change mean that the SQL class violates the SRP

- We can spot this SRP violation from a simple organizational standpoint
- The method outline of SQL shows that there are private methods, such as `SelectWithCriteria`, that apepar to relate only to `select` statements

- Private method behavior that applies only to a small subset of a class can be a useful heuristic for spotting potential areas for improvement
- However, the primary spur for taking action should be the system change itself
- If the SQL class is deemed logically complete, then we dont need to worry about separating the responsibilities
- If we won't need `update` functionality for the forseeable future, then we should leave SQL alone
- But as soon as we find ourselves opening up a class, we should consider fixing our design

- What if we considered the solution like that in Listing 10-10?
- Each public interface method defined in the previous SQL from Listing 10-9 is refactored out to its own derivative of the SQL class
- Note that the private methods, such as `ValuesList`, move directly where they are needed
- The common private behvaior is isolated to a pair of utility classes, `where` and `ColumnList`

Listing 10-10 A set of closed classes

```
“abstract public class Sql {
      public Sql(String table, Column[] columns)
      abstract public String generate();
   }

   public class CreateSql extends Sql {
      public CreateSql(String table, Column[] columns)
      @Override public String generate()
   }

   public class SelectSql extends Sql {
      public SelectSql(String table, Column[] columns)
      @Override public String generate()
   }

   public class InsertSql extends Sql {
      public InsertSql(String table, Column[] columns, Object[] fields)
      @Override public String generate()
      private String valuesList(Object[] fields, final Column[] columns)
   }

   public class SelectWithCriteriaSql extends Sql {
      public SelectWithCriteriaSql(
      String table, Column[] columns, Criteria criteria)
      @Override public String generate()
   }

   public class SelectWithMatchSql extends Sql {
      public SelectWithMatchSql(String table, Column[] columns, Column column, String pattern)
      @Override public String generate()
   }

   public class FindByKeySql extends Sql
      public FindByKeySql(
         String table, Column[] columns, String keyColumn, String keyValue)
      @Override public String generate()
   }

   public class PreparedInsertSql extends Sql {
      public PreparedInsertSql(String table, Column[] columns)
         @Override public String generate() {
      private String placeholderList(Column[] columns)
   }

   public class Where {
      public Where(String criteria)
      public String generate()
   }

   public class ColumnList {
      public ColumnList(Column[] columns)
      public String generate()
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- The code in each class becomes excruciatingly simple
- Our required comprehension time to understand any class decreases to almost nothing
- The risk that one function could break another becomes very small
- From a test standpoint, it becomes an easier task to prove all bits of logic in this solution, as the classes are all isolated from one another

- Equally important, when it's time to add the `update` statements, none of the existing classes need change
- We code the logic to build `update` statements in a new subclass of `SQL` named `UpdateSQL`
- No other code in the system will break b/c of this chagne

- Restructured SQL logic represents best of all words
- It supports SRP
- Supports Open-Closed Principle
- Our restructured SQL class is open to allow new functionality via subclassing, but we can make this change while keeping every other class closed
- Simply drop our `UpdateSQL` class in place

- We want to structure our systems so that we muck with as little as possible when we update them w/ new or changed features
- In ideal system, we incorporate new features by extending the system, not by making modifications to existing code

##### Isolation from Change

- Needs will change so code will change
- There are concrete classes (which contain implementation details)
- And there are abstract classes which represent details only
- We introduce interfaces and abstract classes to help isolate the impact of those details
- A client class depending upon concrete details is at risk when those details change
- We can introduce interfaces and abstract classes to help isolate the impact of those details

- Dependencies upon concrete details create challenges for testing our system
- If we're building a `Portfolio` class and it depends upon an external `TokyoStockExchange` API to derive the portfolio's value, our test cases are impacted by the volatility of such a lookup
- `Hard to write a test when we get a different answer every 5 minutes`

- Instead of designing `Portfolio` so that it directly depends upon `TokyoStockExchange`, we create an interface, `StockExchange` that declares a single method

```
“public Portfolio {
      private StockExchange exchange;
      public Portfolio(StockExchange exchange) {
         this.exchange = exchange;
      }
   // …
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- Now our test can create a testable implementation of `StockExchange` interface that emulates the `TokyoStockExchange`
- This test implementation will fix the current value for any symbol we use in testing
- If our test demonstrates purchasing 5 shares of Microsoft for our portfolio, we code the test implementation to always return $100 per share of Microsoft
- Our test implementation of the `StockExchange` interface reduces to a simple table lookup
- We can then write a test that expects $500 for our overall portfolio value

```
“public class PortfolioTest {
      private FixedStockExchangeStub exchange;
      private Portfolio portfolio;

      @Before
      protected void setUp() throws Exception {
        exchange = new FixedStockExchangeStub();
        exchange.fix(”MSFT”, 100);
        portfolio = new Portfolio(exchange);
      }
      
      @Test
      public void GivenFiveMSFTTotalShouldBe500() throws Exception {
        portfolio.add(5, ”MSFT”);
        Assert.assertEquals(500, portfolio.value());
      }
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- If a system is decoupled enough to be tested in this way, it will also be more flexible and promote more reuse
- The lack of couping means that the elements of our system are better isolated from each other and from change
- This isolation is easy to understand each element of the system

- Minimizing coupling in this way, our classes adhere to another class design known as `Dependency Inversion Principle`
- DIP says that our classes should depend upon abstractions, not concrete details

- Instead of being dependent upon the implementation deatils of the `TokyoStock-Exchange` class, our `Portfolio` class is now dependent upon the `StockExchange` interface
- The `StockExchange` interface represents the abstract concept of asking for the current price of a symbol
- This abstraction isolates all of the specific of obtaining such a price, including from where that price is obtained