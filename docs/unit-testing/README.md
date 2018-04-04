
# Unit testing

## Why we test

- To validate that our code does what we think it does.
- To enable us to change existing code with confidence.

## Standards
- [Test method names should clearly indicate what they are testing](test-method-names-should-clearly-indicate-what-they-are-testing.md).
- [Tests should be written as if they are a specification](tests-should-be-written-as-if-they-are-a-specification.md).
- [Tests should be short and simple](tests-should-be-short-and-simple.md)
- [Use TestCases appropriately](use-test-cases-appropriately.md)
- [Use test cases to test boundary conditions](use-test-cases-to-test-boundary-conditions.md)
- [Keep functions pure if possible](keep-functions-pure-if-possible.md)
- [Be wary of refactoring tests](be-wary-of-refactoring-tests.md)
- [Prefer black box over white box testing](prefer-black-box-over-white-box-testing.md)
- [Only test the public interface](only-test-the-public-interface.md)

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
