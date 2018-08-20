## Follow the testing pyramid

> The test pyramid is a way of thinking about [how] different kinds of automated tests should be used to create a balanced portfolio. Its essential point is that you should have many more low-level UnitTests than high level [E2E tests] running through a GUI - [Martin Fowler](https://martinfowler.com/bliki/TestPyramid.html)

![Testing pyramid](https://i.imgur.com/F4HtHsK.jpg)

### Unit tests 
Tests a single piece of functionality isolated from the rest of the system.
- Cheapest to write.
- Easiest to maintain
- Fastest to run: in-memory only, mock out any external dependencies
- Write lots. Go nuts.

### Integration tests
Test that multiple units work correctly together. Certain parts of the system may still be mocked.
- May be more expensive to write, run slower, and more difficult to maintain than unit tests, as more of the system is being tested at one time.
- Depending on requirements, may call out to external resources.
- For GUI / web apps, may or may not test through the GUI. 
- It is sometimes easier to test one component through another, for instance if the first component computes complex input for the system under test.
- Write fewer integration tests than unit tests.

### E2E tests
Full application stack tested as a whole, including front end, database and all other external resources.
- Most difficult to write, as often complex data setup is required.
- Slowest to run, as we make real DB and web-service calls, have to wait for pages to load, etc.
- Brittle, as we rely on many external systems that may not be directly under our control.
- Concentrate such tests on a few business critical happy paths, and/or scenarios that are otherwise hard to test at lower levels of testing.
- Tests may represent entire customer journeys through the site, from login to purchase.
- Nearest thing to a real user testing our site.
