- Code should be nicely formatted

###### Purpose of Formatting

- Code formatting is important
- Code formatting is about communication, and communication is the professional dev's first order of business

- Coding style and readability set precedents that continue to affect maintainability and extensibility long after the original code has been changed beyond recognition

##### Vertical Formatting

######## Newspaper Metaphor

- Read newspaper vertically
- At the top, you expect headline that will tell you what the story is about and allows you to decide whether it is something you want to read
- First paragraph gives you a synopsis of the whole story, hiding all details while giving you the broad concepts
- As you continue downard, the details cinrease until you have all the dates, names, quotes, claims, and the like

- We would like a source file to be like a newspaper article
- Name should be simple but explanatory
- The name, by itself, should be sufficient to tell us whether we are in the right module or not
- Topmost parts of the soruce file should provide high-level concepts and algorithms
- Details should increase asd we move downwards until at the end we find the lowest level functions and details in the source file

- Newspaper is composed of many articles; most are very small
- Some are a bit larger
- Very few contain as much text as a page can hold
- This makes the newspaper `usable`
- If newspaper were just one long story containing a disorganized agglomeration of facts, dates, and names, then we would not read it

###### Vertical Openness Between Concepts

- Nearly all code is read left to right and top to bottom
- Each line represents an expression or clause, and each group of lines represents a complete though
- Those thoughts should be separated from each other with blank lines

- Consider listing 5-1
- There are blank lines that separate the package declaration, the import(s), and each of the functions.
- This extremely simple rule has a profound effect on the visual layout of the code
- Each blank line is a visual cue that identifies a new and separate concept

```
Listing 5-1 BoldWidget.java
   package fitnesse.wikitext.widgets;

   import java.util.regex.*;

   public class BoldWidget extends ParentWidget {
     public static final String REGEXP = “’’’.+?’’’”;
     private static final Pattern pattern = Pattern.compile(“’’’(.+?)’’’”,
       Pattern.MULTILINE + Pattern.DOTALL
     ); 

     public BoldWidget(ParentWidget parent, String text) throws Exception {
       super(parent);
       Matcher match = pattern.matcher(text);
       match.find();
       addChildWidgets(match.group(1));
    }

    public String render() throws Exception {
      StringBuffer html = new StringBuffer(“<b>”);
      html.append(childHtml()).append(“</b>”);
      return html.toString();
   }

  }

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks.

```

###### Vertical Density

- If openness separates concepts, then vertical density implies close association
- So lines of code that are tightly related should appear vertically dense
- Notice how the useless comments in Listing 5-3 break the close association of the two instance variables

```
“Listing 5-3
   public class ReporterConfig {

     /**
      * The class name of the reporter listener
      */
     private String m_className;

     /**
      * The properties of the reporter listener
      */
     private List<Property> m_properties = new ArrayList<Property>();

     public void addProperty(Property property) {
       m_properties.add(property);
     }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 

```

- Listing 5-4 is much easier to read
- Can look and see this is a class w/ two variables and a method

```
“Listing 5-4
   public class ReporterConfig {
     private String m_className;
     private List<Property> m_properties = new ArrayList<Property>(); 

     public void addProperty(Property property) {
       m_properties.add(property);
     }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

###### Vertical Distance

- Concepts that are closely related should be kept vertically close to each other
- This rule doesn't work for concepts that belong in separate files
- But then closely related concepts should not be separated into different files unless you have a very good reason

- For those concepts that are so closely related that they belong in the source file, their vertical separation should be a measure of how important each is to the understandability of the other
- We want to avoid forcing our readers to hop around through our source files and classes

###### Variable Declarations

- Variables should be declared as close to their usage as possible
- B/c our functions are very short, local variables should appear at the top of each function

```
“private static void readPreferences() {
     InputStream is= null;
     try {
       is= new FileInputStream(getPreferencesFile());
       setPreferences(new Properties(getPreferences()));
       getPreferences().load(is);
     } catch (IOException e) {
       try {
         if (is != null)
           is.close();
       } catch (IOException e1) {
       }
     }
  }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- Control variables for loops should usually be declared within the loop statement

```
“public int countTestCases() {
     int count= 0;
     for (Test each : tests)
       count += each.countTestCases();
     return count;
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

######## Instance variables

- Should be declared at the top of the class
- This should not increase the vertical distance of these variables, b/c in a well-designed class, they are used by many, if not all, of the methods of the class

######## Dependent Functions

- If one function calls another, they should be vertically close, and the caller should be above the callee, if at all possible
- This gives the program a natural flow
- If convention is followed reliabily, readers will be able to trust that function definitons will follow shortly after their use

```
“Listing 5-5 WikiPageResponder.java
   public class WikiPageResponder implements SecureResponder {
     protected WikiPage page;
     protected PageData pageData;
     protected String pageTitle;
     protected Request request;
     protected PageCrawler crawler;”

“public Response makeResponse(FitNesseContext context, Request request)
       throws Exception {
       String pageName = getPageNameOrDefault(request, “FrontPage”);
       loadPage(pageName, context);
       if (page == null)
         return notFoundResponse(context, request);
       else
         return makePageResponse(context);
     }

     private String getPageNameOrDefault(Request request, String defaultPageName)
     {
       String pageName = request.getResource();
       if (StringUtil.isBlank(pageName))
         pageName = defaultPageName;

       return pageName;
     }

     protected void loadPage(String resource, FitNesseContext context)
       throws Exception {
       WikiPagePath path = PathParser.parse(resource);
       crawler = context.root.getPageCrawler();
       crawler.setDeadEndStrategy(new VirtualEnabledPageCrawler());
       page = crawler.getPage(context.root, path);”
       if (page != null)
         pageData = page.getData();
     }

     private Response notFoundResponse(FitNesseContext context, Request request)
       throws Exception {
       return new NotFoundResponder().makeResponse(context, request);
     }

     private SimpleResponse makePageResponse(FitNesseContext context)
       throws Exception {
       pageTitle = PathParser.render(crawler.getFullPath(page));
       String html = makeHtml(context);

       SimpleResponse response = new SimpleResponse();
       response.setMaxAge(0);
       response.setContent(html);
       return response;
     }
   …”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- This snippet provides a good example of keeping constants at the appropriate level
- The `FrontPage` construct constant could have been buried in the `getPageNameOrDefault` function, but that would have hidden a well known and expected constant in an inappropriately low-level function
- Better to pass that constant down from the place where it makes sense to know it to the place that actually uses it

######## Conceptual Affinity

- Certain bits of code want to be near other bits
- The stronger the affinity, the less vertical distance there should be between them

- This affinity might be based on direct dependence, such as one function calling another, or a function using a variable
- Affinity might also be caused b/c a group of functions perform a similar opertaion

```
“ public class Assert {
     static public void assertTrue(String message, boolean condition) {
       if (!condition)
         fail(message);
     }

     static public void assertTrue(boolean condition) {
       assertTrue(null, condition);
     }

     static public void assertFalse(String message, boolean condition) {
       assertTrue(message, !condition);
     }

     static public void assertFalse(boolean condition) {
       assertFalse(null, condition);
     }
   …”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- These functions have a strong conceptual affinity b/c they share a common naming sheme and perform variations of the same basic tasks
- Even if they didn't call each other, they would still want to be close together

######## Vertical Ordering

- In general, we want fucntion call dependencies to point in the downward direction
- `A function that is called should be below a function that does the calling`
- This creates a nice flow down the source code module from high level to low level

- As in newspaper articles, we expect the most important concepts to come first, and we expect them to be expressed w/ the least amount of polluting detail

###### Horizontal Formatting

######## Horizontal Openness and Density

- Use horizontal white space to associate things that are strongly related and disassociate things that are more weakly related
- Consider following function:
```
“ private void measureLine(String line) {
     lineCount++;
     int lineSize = line.length();
     totalChars += lineSize;
     lineWidthHistogram.addLine(lineSize, lineCount);
     recordWidestLine(lineSize);
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- Surrounded the assignment operators with space to accentuate them
- Assignment statements ahve two distinct and major elements: the left side and the right side
- The spaces make that separation obvious

- On the other hand, no spaces btwn function names and opening parenthesis
- This is b/c function and its arguments are closely related
- Separating them makes them appear disjointed instead of conjoined
- Separate arugments within the function call parenthesis to accentuate the comma and show that the arguments are separate

- Another use for white space is to accentuate the precedence of operators

```
“ public class Quadratic {
     public static double root1(double a, double b, double c) {
       double determinant = determinant(a, b, c);
       return (-b + Math.sqrt(determinant)) / (2*a);
     }

     public static double root2(int a, int b, int c) {
       double determinant = determinant(a, b, c);
       return (-b - Math.sqrt(determinant)) / (2*a); 
     }

     private static double determinant(double a, double b, double c) {
       return b*b - 4*a*c;
     }
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- The factors have no white space btwn them b/c they are high precedence
- The terms are separated by white space b/c addition and subtraction are lower precedence
- Notice how nicely the equations read

######## Horizontal alignment

```
public class FitNesseExpediter implements ResponseSender
   {
     private Socket socket;
     private InputStream input;
     private OutputStream output;
     private Request request;

     private Response response;
     private FitNesseContext context;
     protected long requestParsingTimeLimit;
     private long requestProgress;
     private long requestParsingDeadline;
     private boolean hasError;

     public FitNesseExpediter(Socket s, FitNesseContext context) throws Exception
     {
       this.context = context;
       socket = s;
       input = s.getInputStream();
       output = s.getOutputStream();
       requestParsingTimeLimit = 10000;
     }

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks.
```

######## Indentation

- A source file is a hierarchy like an outline
- There is info that pertains to the file as a whole,
    * to the individual classes within the file
    * to the methods within the classes
    * to the blocks within the methods
    * and recursively to the blocks within the blocks
- Each level of this hierarchy is a scope into which names can be declared and in which declarations and executable statements are interpreted

- To make this hierarchy of scopes visible, we indent the lines of source code in proportion to their position in the hiearchy
- Statements at the top level of the file, such as most class declarations, are not indented at all

######## Breaking Indentation

- Sometimes tempting to break the indentation rule for short if statements, short while loops, or short functions

```
“public class CommentWidget extends TextWidget
   {
     public static final String REGEXP = “^#[^\r\n]*(?:(?:\r\n)|\n|\r)?”;

     public CommentWidget(ParentWidget parent, String text){super(parent, text);}
     public String render() throws Exception {return “”; }
   }
```

- Prefer to expand and indent the scopes instead, like this:
```
   public class CommentWidget extends TextWidget {
     public static final String REGEXP = “^#[^\r\n]*(?:(?:\r\n)|\n|\r)?”

     public CommentWidget(ParentWidget parent, String text) {
       super(parent, text);
     }

     public String render() throws Exception {
       return “”;
     }
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

######## Dummy Scopes

- Sometimes the body of a `while` or `for` statement is a dummy as shown below
- Should structure it like this

```
“while (dis.read(buf, 0, readBufferSize) != -1)     ;”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```
- Note the space before semicolon

###### Team Rules

- A team of developers should agree upon a single formatting style, and then every member should use your style
- Software should have a consistent style

- Reader of code needs to be able to trust that the formatting gestures he/she has seen in one source file will mean the same thing in others
- Don't want to add additional complexity to the source code by writing it in a jumble of different individual styles

###### Uncle Bob's Formatting Rules

```
public int getWidestLineNumber() {
     return widestLineNumber;
   }

   public LineWidthHistogram getLineWidthHistogram() {
     return lineWidthHistogram;
   }

   public double getMeanLineWidth() {
     return (double)totalChars/lineCount;
   }

   public int getMedianLineWidth() {
     Integer[] sortedWidths = getSortedWidths();
     int cumulativeLineCount = 0;
     for (int width : sortedWidths) {
       cumulativeLineCount += lineCountForWidth(width);
       if (cumulativeLineCount > lineCount/2)
         return width;
     }
     throw new Error(“Cannot get here”);
   }

   private int lineCountForWidth(int width) {
     return lineWidthHistogram.getLinesforWidth(width).size();
   }

   private Integer[] getSortedWidths() {
     Set<Integer> widths = lineWidthHistogram.getWidths();
     Integer[] sortedWidths = (widths.toArray(new Integer[0]);
        Arrays.sort(sortedWidths);
        return sortedWidths;
      }
   }

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks.

```

