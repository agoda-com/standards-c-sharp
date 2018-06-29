
**Guiding principle: Simplicity over complexity.**

> _Keep It Simple, Stupid_ - Some guy

## Infrastructure

- [Use a simple web solution structure](infrastructure/web-solution-structure.md)
- [Register dependencies using attributes](infrastructure/attribute-based-registration.md)
- [Service design guide](infrastructure/service-design.md)
- [Do not depend on framework abstractions](infrastructure/framework-abstractions.md)

## Methods
#### Static and instance methods
- [Prefer static methods over instance](static-methods/prefer-static-methods.md)
- [When to write a static method](static-methods/when-static.md)
- [When to write an instance method](static-methods/when-instance.md)
#### Extension methods
- [General guidance](extension-methods/general.md)
- [When (and when not) to write an extension method](extension-methods/when.md)

## Exceptions
#### Throwing exceptions
- [Fail as quickly and as loudly as possible](exceptions/fail-as-quickly-and-loudly-as-possible.md)
- [Only throw exceptions in exceptional circumstances](exceptions/only-throw-exceptions-in-exceptional-circumstances.md)
#### Catching exceptions
- [Be specific in what you catch](exceptions/be-specific-in-what-you-catch.md)
- [Only catch exceptions with good reason](exceptions/only-catch-exception-with-good-reason.md)
- [Make use of the global exception handler](exceptions/global-exception-handler.md)
#### Swallowing exceptions
- [Critical vs non-critical exceptions](exceptions/critical-vs-non-critical-exceptions.md)
- [Don't swallow exceptions unless you can gracefully recover](exceptions/dont-swallow-exceptions-unless-you-can-gracefully-recover.md)
#### Rethrowing exceptions
- [How to rethrow an exception from a catch block](exceptions/how-to-rethrow-an-exception-from-a-catch-block.md)
- [Rethrow exceptions when you cannot gracefully recover](exceptions/rethrow-exceptions-when-you-cannot-gracefully-recover.md)

## Testing

- [Why we test](unit-testing/why.md)
- [Test method names should clearly indicate what they are testing](unit-testing/test-method-names-should-clearly-indicate-what-they-are-testing.md)
- [Tests should be written as if they are a specification](unit-testing/tests-should-be-written-as-if-they-are-a-specification.md)
- [Tests should be short and simple](unit-testing/tests-should-be-short-and-simple.md)
- [Use test cases appropriately](unit-testing/use-test-cases-appropriately.md)
- [Use test cases to test boundary conditions](unit-testing/use-test-cases-to-test-boundary-conditions.md)
- [Keep functions pure if possible](unit-testing/keep-functions-pure-if-possible.md)
- [Be wary of refactoring tests](unit-testing/be-wary-of-refactoring-tests.md)
- [Prefer black box over white box testing](unit-testing/prefer-black-box-over-white-box-testing.md)
- [Only test the public interface](unit-testing/only-test-the-public-interface.md)
- [Create reusable concrete mocks](unit-testing/create-reusable-concrete-mocks.md)
- [Consider using AutoFixture](unit-testing/consider-using-autofixture.md)
- [Further reading](https://github.com/jpreese/CSharpUnitTestingBestPractices)

## Collections
#### Choosing the correct type
- [Choose the correct general collection implementation](collections/choosing-collection-implementation.md)
- [Collections on public APIs](collections/public-api-types.md)
- [Collections on non-public API methods](collections/non-public-api-types.md)
- [Do not use Arrays, except...](collections/arrays.md)
- [Queues](collections/queues.md)
- [IQueryable](collections/iqueryable.md)
#### General
- [Do not return null to represent an empty enumerable](collections/null-empty-enumerables.md)
- [Specify the initial capacity of a collection when known](collections/specify-collection-size.md)
- [Do not check for presence of elements before enumerating a collection](collections/dont-check-collection-size.md)

## Code style
- [Monads](code-style/monads.md)
- [Reflection](code-style/reflection.md)
- [Dyanmics](code-style/dynamics.md)
- [#regions](code-style/regions.md)


## Logging
- [Logging](logging/general.md)
- [Log levels](logging/log-levels.md)

