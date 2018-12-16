- There is a reason that we keep our variables private
- Don't want anyone else to depend on them
- Want to keep the freedom to change their type or implementation on a whim or impulse

###### Data Abstraction

- Consider difference btwn Listing 6-1 and Listing 6-2
- Both reperesent the data of a point on the Cartesian plan
- One exposes its implementation and the other completely hides it

```
Listing 6-1 Concrete Point
public class Point {
     public double x;
     public double y;
   }

Listing 6-2 Abstract Point
   public interface Point {
     double getX();
     double getY();
     void setCartesian(double x, double y);
     double getR();
     double getTheta();
     void setPolar(double r, double theta);
   }

```

- Beautiful thing about Listing 6-2 is that there is no way you can tell whether the implementation is in rectangular or polar coordinates
- It may be neither
- Yet the interface still unmistakebly represents a data structure

- It represents more than just a data structure
- The methods enforce an access policy
- You can read the individual coordinates independently, but you must set the coordinates together as an atomic operation

- Listing 6-1, on the other hand, is very clearly implemented in rectangular coordinates, and it forces us to manipulate those coordinates independently
- This exposes implementation
- It would expose implementation even if the variables were private and we were using single variable getters and setters

- Hiding implementation is not just a matter of putting a layer of functions btwn variables
- Hiding implementation is about abstractions
- A class does not simply push its variables out through getters and setters
- Rather it exposes abstract interfaces that allow its users to manipulate the `essense` of the data, w/o having to know its implementation

- Consider Listing 6-3 and Listing 6-4
- The first uses concrete terms in communicate the fuel level of a vehicle
    * The second one does so w/ the abstraction of percentage
- In concrete case you can be pretty sure that these are just accessors of variables
- In the abstract case you have no clue at all about the form of the data

```
Listing 6-3 Concrete Vehicle
   public interface Vehicle {
     double getFuelTankCapacityInGallons();
     double getGallonsOfGasoline();
   }
```

```
Listing 6-4 Abstract Vehicle
   public interface Vehicle {
     double getPercentFuelRemaining();
   }
```

- In both of the above cases, the section option is preferable
- Do not want to expose the details of our data
- Want to express our data in abstract terms
- No merely accomplushed by using interfaces and/or getters and setters
- Serious thought needs to be put into the best way to represent the data that an object contains
- Worst option is to blithely add getters and setters

###### Data/Object Anti-Symmetry

- Two examples below show difference btwn objects and data structures
- `Objects hide their data behind abstractions and expose functions that operate on data`
- `Data structures expose their data and have no meaningful functions`
- Notice complimentary nature
- They are virtual opposites

- Consider the procedural shape example in Listing 6-5
- `Geometry` class operates on the 3 shape classes (`Shape`, `Rectangel`, and `Circle`)
- The shape classes are simple data structures w/o any behavior
- All the behavior is in the `Geometry` class

```
Listing 6-5 Procedural Shape
   public class Square {
     public Point topLeft;
     public double side;
   }

   public class Rectangle {
     public Point topLeft;
     public double height;
     public double width;
   }

   public class Circle {
     public Point center;
     public double radius;
   }

   public class Geometry {
     public final double PI = 3.141592653589793;

     public double area(Object shape) throws NoSuchShapeException 
     {
       if (shape instanceof Square) {
         Square s = (Square)shape;
         return s.side * s.side;
       }

       else if (shape instanceof Rectangle) {
         Rectangle r = (Rectangle)shape;
         return r.height * r.width;
       }
       else if (shape instanceof Circle) {
         Circle c = (Circle)shape;
         return PI * c.radius * c.radius;
       }
       throw new NoSuchShapeException();
     }
   }

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks.

```

- OO programmers might complain that this is procedural - and they'd be right
- But consider what would happen if the `perimeter()` function were added to `Geometry`
- `The shape classes would be unaffected`
- `Any other classes that depended upon shapes would also be unaffected`
- On the other hand, if I add a new shape, all functions in `Geometry` need to change to deal with it
- Notice that these are diametrically (directly/completely) opposed

- Now consider the OO solution in Listing 6-6
- The `area()` method is polymorphic
- No `Geometry` class is necessary
- `So if new shape is added, none of the existing functions are affected`
    * `But if new function added, then all of the shapes must be changed`

```
Listing 6-6 Polymorphic Shapes
   public class Square implements Shape {
     private Point topLeft;
     private double side;

     public double area() {
       return side*side;
     }
   }

   public class Rectangle implements Shape {
     private Point topLeft;
     private double height;
     private double width;
  
     public double area() {
       return height * width;”
     }
   }

   public class Circle implements Shape {
       private Point center;
       private double radius;
       public final double PI = 3.141592653589793;

       public double area() {
         return PI * radius * radius;
       }
   }

```

- There are ways around this that are well known to experienced OO designers: `VISITOR` or `dual-dispatch` for example
- But these techniques carry costs of their own and generally return the structure to that of a procedural program

- Again, we see the complimentary nature of these two definitions; they are virtually opposite
- `This exposes the fundamental dichotomy btwn objects and data structures`

```
Procedural code (code using data structures) makes it easy to add new functions w/o changing the existing data structures.
OO code, makes it easy to add new classes w/o changing existing functions
```

- The complement is also true

```
Procedural code makes it hard to add new data structures b/c all the functions must change
OO code makes it hard to add new functions b/c all the classes must change
```

- So the things that are hard for OO are easy for procedures, and the things that are hard for procedures are easy for OO

- In any complex system there are going to be times when we want to add new data types rather than new functions
- For these cases, objects and OO are most appropriate
- On the other hand, there will also be times when we'll want to add new functions as opposed to data types
- In that case procedural code and data structures will be more appropriate

- Mature programmers know that the idea that everythign is an object is a myth
- Sometimes you really do want simple data structures w/ procedures operating on them

###### Law of Demeter

- A module should not know about the innards of the objects it manipulates
- As we saw in the last section, objects hide their data and expose operations
- This means that an object should not expose its internal structure through accessors b/c to do so is to expose, rather than hide, its internal strucure

- More precisely, the Law of Demeter says that a method `f` of class `C` should only call the methods of these
    * C
    * An object created by f
    * An object passed as an argument to f
    * An object held in an instance variable of C

- The method should NOT invoke methods on objects that are returned by any of the allowed functions
- In other words, talk to friends, not to strangers

- Following code appears to violate this rule (among other things) b/c it calls the `getScratchDir()` function on the return value of `getOptions()` and then calls `getAbsolutePath()` on the return value of `getScratchDir()`

```
final String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();
```

###### Train Wrecks

- This kind of code is often called a `train wreck` b/c it looks like a bunch of couple train cars
- Chains of calls liek this are generally considered to be sloppy style and should generally be avoided
- It is best to split them up as follows

```
Options opts = ctxt.getOptions();
File scratchDir = opts.getScratchDir();
final String outputDir = scratchDir.getAbsolutePath();
```

- Are these two snippest of code violations of the Law of Demeter
- The containing module knows that the `ctxt` object contains options, which contain a scratch directory, which has an absolute path
    * That's a lot of knowledge for one function to know
- The calling function knows how to navigate through a lot of different objects

- Whether this is a violation of the law depends on whether or not `ctxt`, `Options`, and `ScratchDir` are objects or data structures
- If they are objects, their internal structure should be hidden rather than exposed
    * So knowledge of their innards is a clear violation of the law of demeter
- On the other hand, if `ctxt`, `Options`, and `ScratchDir` are just data structures w/ no behavior, then they naturally expose their internal structure
    * So the law does not apply

- Use of accessor functions confuses the issue
- If the code had been written as follows, then we probably wouldn't be asking about violations

```
final String outputDir = ctxt.options.scratchDir.absolutePath;
```

- Issue would be a lot less confusing if data structures simply had public variables and no functions, whereas objects had private variables and public functions
- However, there are frameworks and standards that demand that even simple data structures have accessors and mutators

###### Hybrids

- This confusion sometimes leads to hybrid structures that are half object and half data structure
- They have functions to do significant things, and they also have either public variables or public accessors and mutators that make private variables public, tempting other external functions to use those variables the way that a procedural program would use a data structure (this is called Feature Envy in refactoring)

- `Hybrids make it hard to add new functions but also make it hard to add new data structures`
- Worst of both words
- Avoid creating them
- Indicative of a muddled design whose authors are unsure of whether they need protection from functions or types

###### Hiding Structure

- What if `ctxt`, `options`, and `scratchDir` are objects with real behavior?
-  B/c objects are supposed to hide their internal structure, we should not be able to navigate through them
- How then would we get the absolute path of the scratch directory?

```
ctxt.getAbsolutePathOfScratchDirectoryOption();

or

ctx.getScratchDirectoryOption().getAbsolutePath()
```

- First option could lead to an explosion of methods in the ctxt object
- Second presumes that `getScratchDirectoryOption()` returns a data structure, not an object

- If `ctxt` is an object, we should be telling it to do something
- Should not be asking it about its internals
- So why do we want the absolute path of the scratch directory?
- What are we doing with it?
- Consider this code from the same module

```
String outFile = outputDir + “/” + className.replace('.', '/') + “.class”;
FileOutputStream fout = new FileOutputStream(outFile);
BufferedOutputStream bos = new BufferedOutputStream(fout);
```

- Admixture of different levels of detail is troubling
- Dots, slashes, file extensions, and `File` objects should not be so carelessly mixed together, and mixed with enclosing code
- Ignoring that, we see the intent of getting the absolute path of the scratch director was to create a scratch file of a given name

- What if we told `ctxt` object to do this?

```
BufferedOutputStream bos = ctxt.createScratchFileStream(classFileName);

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

- This allows `ctxt` to hide its internals and prevents the current function from having to violate the Law of Demeter by navigating through objects it shouldn't know about

###### Data Transfer Objects

- Quintessential form of a data structure is a class w/ public variables and no functions
- This is sometimes called a data transfer object, or `DTO`
- DTOs are useful structures, especially when communicating with databases or parsing message from sockets, and so on
- They often become the first in a series of translation stages that convert raw data in a database into objects in the application code

- Somewhat more common is the `bean` form shown in Listing 6-7
- Beans have private variables manipulated by getters and setters

```
Listing 6-7 address.java
   public class Address {
     private String street;
     private String streetExtra;
     private String city;
     private String state;
     private String zip;

     public Address(String street, String streetExtra, 
                     String city, String state, String zip) {
       this.street = street;
       this.streetExtra = streetExtra;
       this.city = city;
       this.state = state;
       this.zip = zip;
     }

     public String getStreet() {
       return street;
     }

     public String getStreetExtra() {
       return streetExtra;
     }

     public String getCity() {
       return city;
     }

     public String getState() {
       return state;
     }

    public String getZip() {
      return zip;
    }
   }

Excerpt From: Robert C. Martin. “The Robert C. Martin Clean Code Collection (Collection).” iBooks. 
```

######## Active Record

- Active records are special forms of DTOs
- They are data structures with public (or bean-accessed) variables; but they typically have navigational methods like `save` and `find`
- Typically these Active Records are direct translations from database tables, or other data sources

- Unfortunately, we often find that devs try to treat these data structures as though they were objects by putting business rule methods in them
- This is awkward b/c it creates a hybrid btwn a data structure and an object

- Solution is to treat Active Record as a data structure as a data structure and to create separate objects that contain the business rules and that hide their internal data (which are probably just instanes of the Active Record)

###### Conclusion

- Objects expose behavior and hide data
    * This makes it easy to add new kinds of objects w/o changing existing behavior
- Data structures expose data and have no significant behavior
    * But, also makes it hard to add new behaviors to existing data structures but makes it hard to add new behaviors to existing data structures but make it hard to add new data structures to existing functions

- In any given system, we will sometimes want flexibility to add new data types, so we prefer objects for that part of the system
- Other times we want flexibility to add new behaviors, so in that part we prefer data types and procedures
- Good software developers understand these issues and choose the approach that is best for the job at hand

