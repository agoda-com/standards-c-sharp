
# Unit testing

## Why we test

- To validate that our code does what we think it does.
- To enable us to change existing code with confidence.

## Standards
- [Test method names should clearly indicate what they are testing](test-method-names-should-clearly-indicate-what-they-are-testing.md).
- [Tests should be written as if they are a specification](tests-should-be-written-as-if-they-are-a-specification.md).
- [Tests should be short and simple](tests-should-be-short-and-simple.md)


## More 
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
      - But such classes are much more difficult to refactor as their tests are likely to break if their internals are changed.
- Only ever test the public interface. If a class is difficult to test through its public interface, then this may be a code smell that the class is poorly designed. If _you_ as the author are having trouble using your class, think about your poor consumers.
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
