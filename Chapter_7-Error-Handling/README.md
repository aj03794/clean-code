- Many code bases are dominated by error handling
- Don't mean that error handling is all they do
    * Means that it is nearly impossible to see what the code does b/c of all of the scattered error handling
- `Error handling is important but if it obscures logic, it's wrong`

###### Use Exceptions Rather Than Return Codes

- In the past, there were many languages that didn't have exceptions
- In those languages the techniques for handling and reporting errors were limited
- Code either set an error flag or return an error code that the caller could check

- The problem with these approaches is that they clutter the caller
- The caller must check for errors immediately after the call
- Unfortunately, it's easy to forget
- For this reason it is better to throw an exception when you encounter an error
- This makes calling code cleaner
- Its logic is not obscured by error handling

- Listing 7-2 is an exmaple

```
public class DeviceController {
     …
     
     public void sendShutDown() {
       try {
         tryToShutDown();
       } catch (DeviceShutDownError e) {
         logger.log(e);
       }
   }

    private void tryToShutDown() throws DeviceShutDownError {
      DeviceHandle handle = getHandle(DEV1);
      DeviceRecord record = retrieveDeviceRecord(handle);

      pauseDevice(handle);
      clearDeviceWorkQueue(handle);
      closeDevice(handle);
   }

     private DeviceHandle getHandle(DeviceID id) {
     …
     throw new DeviceShutDownError(“Invalid handle for: ” + id.toString());
     …
    }

    …
   }
```

- Notice how much cleaner it is
- Isn't just a matter of aesthetics
- Code is better b/c two concerns that were tangled, the algorithm for device shutdown and error handling, are now separated

###### Write your Try-Catch-Finally Statement First

- One of the most interesting things about exceptions is that they define a scope within your program
- When you execute code in the `try` portion of a `try-catch-finally` statement, you are stating that execution can abor at any time and then resume at the `catch`

- In a way, `try` blocks are like transactions
- Your `catch` has to leave your program in a consistent state, no matter what happens in the try
- For this reason, it is good practice to start with a `try-catch-finally` statement when you are writing code that could throw exceptions
- This helps you define what the user of that code should expect, no matter what goes wrong with the code that is executed in the `try`

Example
```
“@Test(expected = StorageException.class)
   public void retrieveSectionShouldThrowOnInvalidFileName() {
     sectionStore.retrieveSection(“invalid - file”);
   }
```

The test drives us to create this stub:

```
   public List<RecordedGrip> retrieveSection(String sectionName) {
     // dummy return until we have a real implementation
     return new ArrayList<RecordedGrip>();
   }
```
Our test fails because it doesn’t throw an exception. Next, we change our implementation so that it attempts to access an invalid file. This operation throws an exception:
```
   public List<RecordedGrip> retrieveSection(String sectionName) {
     try {
       FileInputStream stream = new FileInputStream(sectionName)
     } catch (Exception e) {
       throw new StorageException(“retrieval error”, e);
     }
     return new ArrayList<RecordedGrip>();”
  }

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- Our test passes now because we've caught the exception
- Now we can refactor
- We can narrow the type of the exception we catch to match the type that is actually thrown from the `FileInputStream` constructor

- Now that scope has been defined with a `try-catch` structure, we can use TDD to build up the rest of the logic that we need
- That logic will be added btwn the creation of the `FileInputStream` and the `Close`, and can pretend that nothing goes wrong

###### Use Unchecked Exceptions

- Price of checked exceptions is an Open/Closed Principle Violation

###### Provide Context With Exceptions

- Each exception that you throw should provide enough context to determine the source and location of an error

- Create informative error messages and pass them along with your exceptions
- Mention the operation that failed and the type of failure
- If you are logging in your application, pass along enough info to be able to log the error in your `catch`

###### Define Exception Classes in Terms of a Caller's Needs

- Many ways to classify errors
- Can classify them by their source
  * Did they come from one component or another
  * Or their type: Are they device failures, network failures, or programming errors
- Our most important concern should be `how they are caught`

- Example of poor exception classification
- `try-catch-finally` statement for a 3rd party library call

```
“ ACMEPort port = new ACMEPort(12);

   try {
     port.open();
   } catch (DeviceResponseException e) {
     reportPortError(e);
     logger.log(“Device response exception”, e);
   } catch (ATM1212UnlockedException e) {
     reportPortError(e);
     logger.log(“Unlock exception”, e);
   } catch (GMXError e) {
     reportPortError(e);
     logger.log(“Device response exception”);
   } finally {
     …
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- Contains a lot of duplication
- In most exception handling situations, the work that we do is standard regardless of the cause
- Record an error and make sure that we can proceed

- In this case, b/c we know that the work that we are doing is roughly the same regardless of the exception, we can simplify our code by wrapping the API that we are calling and making sure it returns a common exception type

```
“ LocalPort port = new LocalPort(12);
   try {
     port.open();
   } catch (PortDeviceFailure e) {
     reportError(e);
     logger.log(e.getMessage(), e);
   } finally {
     …
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- `Localport` class is just a simple wrapper that catches and translates exceptions thrown by the  `ACMEPort class`

```
“public class LocalPort {
     private ACMEPort innerPort;

     public LocalPort(int portNumber) {
       innerPort = new ACMEPort(portNumber);
     }

     public void open() {
       try {
         innerPort.open();
       } catch (DeviceResponseException e) {
         throw new PortDeviceFailure(e);
       } catch (ATM1212UnlockedException e) {
         throw new PortDeviceFailure(e);
       } catch (GMXError e) {
         throw new PortDeviceFailure(e);
       }
     }
     …
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- Wrappers like the one defined for `ACMEPort` can be very useful
- In fact, wrapping 3rd party APIs is a best practice
- `When you wrap a 3rd party API, you minimize your dependencies upon it: You can choose to move to a different library in the future w/o much penalty`
- Wrapping also makes it easier to mock out 3rd party calls when you are testing your own code

- Final advantage of wrapping is that you aren't tied to a particular vendor's API design choices
- YOu can define an API that you feel comfortable with
- The preceding example, we defined a single exception type for `port` device failure and found that we could write much cleaner code

- Often a single exception class is fine for a particular area of code
- The info sent with the exception can distinguish the errors
- Use different classes only if there are times when you want to catch one exception and allow the other one to pass through

###### Define the Normal Flow

- If you follow advice from the preceding sections, you'll end up w/ a good amount of separation btwn your business logic and your error handling
- Bulk of your code will start to look like a clean unadorned algorithm
- However, the process of doing this pushes error detection to the edges of your program
- You wrap external APIs so that you can throw your own exceptions, and you define a handler above your code so that you can deal with any aborted computation
- Most of the time this is a great approach, but there are sometiems when you may not want to abort

- Example
- Some awkard code that sums expenses in a billing application

```
“ try {
     MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
     m_total += expenses.getTotal();
   } catch(MealExpensesNotFound e) {
     m_total += getMealPerDiem();
   }”

```

- In this business, if meals are expensed, they become part of the total
- If they aren't, the employees get a meal `per diem` amount for that day
- The exception clutters the logic
- It would be better if we didn't have to deal w/ this special case
- Code would much simpler

```
MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
m_total += expenses.getTotal();

```

- Can change the `ExpenseReportDAO` so that it always returns a `MealExpense` object
- If there are no meal expenses, it returns a `MealExpense` object that returns the `per diem` as its total

```
“public class PerDiemMealExpenses implements MealExpenses {
     public int getTotal() {
       // return the per diem default
     }
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- This is called the `SPECIAL CASE PATTERN` (Refactoring by Martin Fowler)
- You create a class or configure an object so that it handles a special case for you
- When you do, the client code doesn't have to deal w/ exceptiona behavior
- The behavior is encapsulated in the special case class

###### Don't Return Null

```
“public void registerItem(Item item) {
     if (item != null) {
       ItemRegistry registry = peristentStore.getItemRegistry();
       if (registry != null) {
         Item existing = registry.getItem(item.getID());
         if (existing.getBillingPeriod().hasRetailOwner()) {
           existing.register(item);
         }
       }
     }
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- This is ball
- When we return `null`, we are essentially creating work for ourselves and foisting problems upon our callers
- All it takes is one missing `null` check to send an application out of control

- Easy to say that the problem w/ the code above s that it is missing a null check, but in actuality, the problem is that it has `too many`
- If you are tempted to return null from a method, consider throwing an exception or returning a `SPECIAL CASE` object instead
- If you are calling a null-returning method from a 3rd party API, consider wrapping that method w/ a method that either throws an exception or returns a special case object

- In many cases, special case objects are an easy remedy
- Imagine code like this

```
“List<Employee> employees = getEmployees();
   if (employees != null) {
     for(Employee e : employees) {
       totalPay += e.getPay();
     }
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- Right now, getEmployees can return `null`, but does it have to?
If we change `getEmployee` so that it returns an empty list, we can clean up the code

```
“List<Employee> employees = getEmployees();
   for(Employee e : employees) {
     totalPay += e.getPay();
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```


```
“ public List<Employee> getEmployees() {
     if( .. there are no employees .. )
       return Collections.emptyList();
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

###### Don't Pass Null

- Returning null from methods is bad, but passing null into methods is worse
- Unless you are working with an API which expects you to pass `null`, you should avoid passing `null` in your code whenever possible

- Example to see why
- Simple method which calculates a metric for two points

```
“public class MetricsCalculator
   {
     public double xProjection(Point p1, Point p2) {
       return (p2.x – p1.x) * 1.5;
     }
     …
   }”

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- What happens when someone passes null as an argument

`calculator.xProject(null, newPoint(12, 13))`

- In java, you will get a `NullPointerException`

- How can we fix it?
- In most programming languages there is no good way to deal w/ a null that is passed by a caller accidentally
- B/c this is the case, the rational approach is to forbid passing `null` by default
- When you do, you can code w/ the knowledge that a `null` in an argument list is an indication of a problem, and end up with far fewer careless mistakes

###### Conclusion

- Clean code is readable, but it must also be robust
- These are not conflicting goals
- We can write robust clean code if we see error handling as a separate concern, something that is viewable independently of our main logic
- To the degree that we are able to do it, we can reason about it independently, and we can make great strides in the maintainability of our code