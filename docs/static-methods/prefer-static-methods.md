## Why prefer static methods

- They are easier to reason about as all state is passed in through arguments.
- They cannot be injected, so we reduce constructor dependencies and container registrations.
- They simplify the code as we don't require an instance to be created.
- They are often easier to test because fewer mocks need to be configured.
