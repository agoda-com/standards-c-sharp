# Unit testing

## Why we test

- To validate that our code does what we think it does.
- To enable us to change existing code with confidence.

## Naming conventions

- Avoid test method names like `MyClassTest()`. What is it actually testing? If it breaks, how do we know what it should be doing?
- Standardized naming conventions:
  - Test class names: `<Classname>Tests` (we pretty much do this already)
  - Test names: `<MethodUnderTest>_<OptionalPreConditions>_<PostCondition>()`
    - eg. `ToggleHarzardLights_WhenAlreadyBlinking_StopsBlinking()`

## Tests as the spec

- Think of the tests as the _specification_ for your application.
- We should be able to tell exactly how a method should behave just by looking at the names (and perhaps the test cases) of its tests. No need to even look at the code, either test or implementation.
- Create one or more tests for each granular requirement.
- Writing tests as spec gives us documentation _for free_. And, unlike Confluence or Word documents, this documentation **can never go out of date**!

## Keep tests small and simple

- Avoid "god" tests where everything about a class is tested in a single method.
  - Keep them simple and focused, testing one requirement a time - a short story, not an epic novel.
  - These kind of tests are much easier to fix if they break.
  - [Bad example](https://github.agodadev.io/agoda-front-end/agoda-com-dictator/blob/cf1af978c6d7b9424a9642094eb9eca952362aab/Src/Agoda.Website/Agoda.Website.UnitTest/Builder/PillViewModelBuilderTests.cs) vs [good example](https://github.agodadev.io/agoda-front-end/agoda-com-dictator/blob/master/Src/Agoda.Website/Agoda.Website.UnitTest/Builder/PillViewModelBuilderTests.cs)
- Overuse of `[TestCase]` can make it difficult to see what is actually being tested, especially when there are many parameters. This often results in a combinatorial explosion as new parameters are added. Test cases are best suited to simple scenarios and boundary conditions. [Good example](https://github.agodadev.io/agoda-front-end/agoda-com-dictator/blob/master/Src/Agoda.Website/Agoda.Website.UnitTest/Builder/PillViewModelBuilderTests.cs#L80)
- Be wary of refactoring tests as this can make them difficult to follow (eg. a `BaseTest`). Sometimes copy+paste is OK. A simple, straight-through test is easier to understand fix if a test breaks. We don't want to waste time trying to understand a bunch of testing infrastructure code first.
- Keep your functions pure if possible. They are easier to test and require less setup.

## Make refactoring easier

- Code must be constantly refactored, else it will deteriorate, and unit tests are vital to enable refactoring with confidence.
- But, there is nothing more depressing than a small refactoring breaking 30 tests.
- So, prefer black box testing over white box.
  - **Black box**: we cannot "see" inside the code being tested - we consider only its input and output, and magic inbetween. This makes refactoring easy, as we are free to change the implementation ("what's inside the box") as long as the output stays the same.
  - **White box**: our test depends upon the implementation, for instance verifying that a certain method was called. Sometimes this is unavoidable, but use sparingly and only when strictly necessary.
      - Classes such as directors or orchestrators, which serve only to call other methods, might be good candidates for white box testing.
      - But such classes are much more difficult to refactor as their tests are likely to break.
- Only ever test the public interface. If a class is difficult to test through its public interface, then this may be a code smell that the class is poorly designed. If you are having trouble using your class, think about your poor consumers.
- Avoid testing internals as, again, this couples the tests to the implementation, meaning they are more likely to break during refactoring.
  - **Never** make a method public just so it can be tested. Each time you do this **multiple** kittens die.
  - Do not use the [`InternalsVisibleToAttribute`](https://docs.microsoft.com/en-us/dotnet/api/system.runtime.compilerservices.internalsvisibletoattribute?view=netframework-4.7.1) for the aforementioned reasons. Won't someone think of the kittens?

## Test file placement

- Traditionally C# tests have been kept in a separate assembly with a parallel folder structure. This has a few downsides:
  - It's difficult to tell at a glance what's tested and what is not.
  - It's not easy to navigate to the test files.
  - The structure of the test assembly must be kept in sync with that of the implementation.
- Client side projects (eg. JS) universally put their tests next to the implementations. This mitigates the problems above.
- So, do not create a separate unit test project. Place the test code beside the classes they are testing. The build process will strip these out from the production build (not yet implemented - POC in progress).

## Mocking

- Dynamic mocks can be messy and repetitive to construct. Consider creating concrete mock implementations, perhaps with an associated builder, to configure and share between tests.

## Misc

- Consider using https://github.com/AutoFixture/AutoFixture to avoid having to think up and create random test data ([example](https://github.agodadev.io/agoda-front-end/agoda-com-dictator/blob/master/Src/Agoda.Website/Agoda.Website.UnitTest/NewSite/Mapper/NHA/SingleRoom/SingleRoomFeatureViewModelMapperTest.cs#L13))
- Further reading: https://github.com/jpreese/CSharpUnitTestingBestPractices
