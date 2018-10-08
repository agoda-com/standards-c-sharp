## When to make an instance method:

- When your method is impure - ie. makes an external call, has side effects like writing to the disk etc. These parts will need to be mocked and injected for unit tests.
- When it requires access to one or more dependencies, again these must be injected into the constructor for mockability.
- When setting up test cases for the static version of the method is time consuming or difficult.
  - In this case, consider writing an instance method to enable injection of mock dependencies to simplify testing.
  - Or, consider keeping the method static but introduce a builder class to help create complex mock data.