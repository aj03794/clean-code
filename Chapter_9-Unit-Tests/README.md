#### Unit Tests

###### The Three Laws of TDD

- `First law: You may not write production code until you have written a failing test`
- `Second law: You may not write more of a unit test than is sufficient to fail`
- `Third law: You may not write more production code than is sufficient to pass the currently failing test`

- These three laws lock you into a cycle that is perhaps 30 seconds long
- The tests and the production code are written `together`, with the tests just a few seconds ahead of the production code

- If we work this way, we will write dozens of tests every day, hundreds of tests every month, and thousands of tests every year
- If we work this way, those tests will cover virtually all of our production code 
- The sheer bulk of those tests, which can rival the size of the production code itself, can present a daunting management problem

###### Keeping Tests Clean

- Having dirty tests is equivalent to, if not worse than, having no tests
- The problem is that tests must change as the production code evolves
- The dirtier the tests, the harder they are to change
- The more tangled the test code, the more likely it is that you will spend more time cramming new tests into the suite than it takes to write the new production code
- As you modify the production code, old tests start to fail, and the mess in the test code makes it hard to get those tests to pass again
- So the tests become viewed as an ever-increasing liability

- Without a test suite, you can NOT ensure that changes to one part of their system did not break other parts of their system
- So defect rates begin to rise
- Production code wil begin to rot

- `Test code is just as important as production code`
- Not a second-class citizen
- Requires thought, design, and care
- Must be kept as clean as production code

######## Tests Enable the -ilities

- If you don't keep your tests clean, you will lose them
- Without them, you lose the very thing that keeps your production code flexible
- It is `unit tests` that keep our code flexible, maintainable, and reasonable
- W/o tests every change is a possible bug
- No matter how flexible your architecture is, no matter how nicely partitioned your design, w/o tests you will be reluctant to make changes b/c of the fear that you will introduce undetected bugs

- But `with` tests that fear virtually disappears
- The higher your test coverage, th eless you fear
- You can make changes with near impunity to code that has less than stellar architecture 
- You can `improve` that architecture and design w/o fear

- Having an automated suite of unit tests that cover the production code is the key to keeping the design and architecture as clean as possible
- Tests enable `change`

- If your tests are dirty, then your ability to change your code is hampered, and you begin to lose the ability to improve the structure of that code
- The dirtier your tests, the dirtier your code becomes
- Eventually you lose the tests, and your code rots

###### Clean Tests

- What makes a clean test?
- Three things: Readability, readability, and readability