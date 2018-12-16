#### Successive Refinement

- Example of a module that started well but did not scale
- Will see how the module was refactored and cleaned

```
public static void main(String[] args) {
    try {
        Args arg = new Args("l, p#, d*", args);
        boolean logging = arg.getBoolean('l');
        int port = arg.getInt('p');
        String directory = arg.getString('d');
        executeApplication(logging, port, directory);
    } catch (ArgsException e) {
        System.out.printf("Argument error: %s\n", e.errorMessage());
    }
}
```

- Just creating an instance of the `Args` class with 2 parameters
- The first parameter is the format, or `schema`, string: "l, p#, d*."
- It defines 3 command line arguments
- The first `-l` is a boolean argument
- The second `-p` is an integer argument
- The third `-d` is a string argument
- The second paramter to the `Args` constructor is simply the array of command-line argument passed into `main`

- If the constructor returns without throwing an `ArgsException` then the incomimng command-line was parsed, and the `Args` instance is ready to be queried
- Methods like `getBoolean`, `getInteger` and `getString` allow us to access the values of the arguments by their names
- If there is a problem, either in the format string or in the command-line arguments themselves, an `ArgsException` will be thrown

###### Args Implementation

```
