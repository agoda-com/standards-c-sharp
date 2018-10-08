## Prefer pure static methods over instance methods

- They are stateless so:
    - are easier to reason about, as all state is passed in as method parameters.
    - are often easier to test, as no state needs to be mocked.
- We reduce constructor dependency bloat. 
- They simplify the code as we don't require an instance to be created.

