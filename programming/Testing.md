Testing
=======

Philosophy
----------

I'm taking some of my philsophy on this from a post at http://blog.stevensanderson.com/2009/11/04/selective-unit-testing-costs-and-benefits/
The basic idea is "just test the important stuff".
The important stuff is basically non-trivial, non-orchestrator code.
Your overarching goal is to isolate all of your non-trivial code from orchestrators (stuff that's dependent on external IO) as much as possible, which allows you to properly test the hard, important stuff.
Basically, good code has a lot of easily testable kernels, and then orchestrators that call those kernels.
So you should end up with good coverage, even with this "test the important stuff" philosophy.

Your company may or may not disagree with this, but this should be the bare minimum of code that you are testing.
If your company requires high coverage, you should still separate your orchestrators, and simply test them as if they are integration tests.
I find this to be an extremely pragmatic approach to how to decide what to test, and how to structure your code.

```
+------------------------------+--------------------------+---------------------------------------------+
|                              | Low cost of unit testing | High cost of unit testing                   |
+------------------------------+--------------------------+---------------------------------------------+
| High benefit of unit testing | Algorithms               | Overcomplicated code (should be refactored) |
+------------------------------+--------------------------+---------------------------------------------+
| Low benefit of unit testing  | Trivial code             | Orchestration code                          |
+------------------------------+--------------------------+---------------------------------------------+
```


Mocking
-------

Mocking is commonly used to patch out external dependencies from code being tested.
This often results in a rats nest where your code is tightly coupled to the tests, for no great reason.
I'm not a fan of using mocks extensively.


Here's some general guidelines for how to avoid using mocks in unit tests:

1. Separate orchestration code from core kernels that need to be tested.
2. More specifically, try to even keep external-data-dependent classes separate from data-independent classes.
3. Keep as little state as possible, especially in classes/objects.
4. Pull data sources up as high as possible. This allows you to test more code w/o having data dependencies lower in the stack.


Sources:
- https://simpleprogrammer.com/back-to-basics-mock-eliminating-patterns/
- https://simpleprogrammer.com/back-to-basics-unit-testing-without-mocks/


Unit test structure
-------------------

It's common for "good programmers" to want to be "good programmers" when writing unit tests.
That is, avoiding code duplication, heavily abstracting code, etc.
That can make unit tests really hard to reason about, however.

Some guidelines to consider (this is just one philosophy):
- Don't rely on helper functions that hide values being tested
- Don't heavily rely on setup methods
- Don't be afraid to copy and paste
- Use long, descriptive test names
- Make the tests themselves small and focused

Sources:
- https://mtlynch.io/good-developers-bad-tests/
