- Clean Code should clearly expose the tensions in the problem to be solved
- Other people should easily be able to enhance it
- There is a difference between code that is easy to read and code that is easy to change

- Clean code is code that has been taken care of
- Someone has taken the time to keep it simple and orderly

- When the same thing is done over and over, it's a sign that there is an idea in our mind that is not well represented in the code
    * Try to figure it out and express that idea more clearly

- Removing duplication and being expressive can take you a very long way into what the author considers clean code

- With clean code the designer makes it look ridiculously simple like all exceptional designs

- The ratio of time spent reading vs writing is well over 10:1
- Constantly reading old code as part of the effort to write new code

- Because of this, we want the code to be easy to read, even if it makes the writing harder
- Making it easy to read actually makes it easier to write

- You cannot write code if you cannot read the surrounding code
- The code you are trying to write today will be hard or easy to write depending on how hard or easy the surrounding code is to read
- So if you want to go fast, if you want to get it done quickly, if you want your code to be easy to write, make it easy to read

###### The Boy Scout Rule

- Not enough to write the code well
- Code has to be kept clean over time
- Code will rot and degrade as time passes
- Must take an active role in preventing this degradation

- If we all checked-in our code a little cleaner than when we checked it out, the code would not rot
- Doesn't have to be a lot
- Change one variable name for the better, break up one function that's a little too large, eliminate one small bit of duplication, clean up one composite `if` statement