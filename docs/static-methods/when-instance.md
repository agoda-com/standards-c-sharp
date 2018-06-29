## When to write an instance method (eg. in an injected component)

- When your method is impure - ie. makes an external call, has side effects like writing to the disk. These parts will need to be mocked for unit tests.
- When it requires one or more (impure) dependencies. These will need to be passed into the constructor for mocking.
- When setting up test cases for the static version is time consuming or difficult. This may, however, indicate a code smell, but there are legitimate cases where this may be required.
  - In this case, an instance method with mock dependencies may ease testing.
  - Do consider keeping the method static but using a builder to help creating the complex mock data.