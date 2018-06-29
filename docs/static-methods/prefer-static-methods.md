## Prefer pure static methods over instance methods

- They are easier to reason about as all state is passed in through arguments.
- We reduce constructor dependencies as they cannot be injected. 
- They simplify the code as we don't require an instance to be created.
- They are often easier to test because fewer mocks and other global state need to be configured.
