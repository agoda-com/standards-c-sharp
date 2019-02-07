
**Guiding principle: Simplicity over complexity.**

> _Keep It Simple, Stupid_ - Some guy

## Project structure and design

- [Keep web solution structure simple](solution-structure/web-solution-structure.md)

## Service design
- [Service design guide](services/service-design.md)
- [Do not depend on framework abstractions](services/framework-abstractions.md)
- [Avoid constructor over injection](services/avoid-constructor-over-injection.md)

## IoC
- [Register dependencies using attributes](di/attribute-based-registration.md)
- [Do not use stereotype-style registrations](di/stereotypes.md)

## Method design

#### Static and instance methods
- [Prefer pure static methods over instance](static-methods/prefer-static-methods.md)
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

- [Why we test](testing/why.md)
- [Follow the testing pyramid](testing/test-pyramid.md)
- [Test method names should clearly indicate what they are testing](testing/test-method-names-should-clearly-indicate-what-they-are-testing.md)
- [Write tests as if they are a specification](testing/tests-as-a-specification.md)

#### Unit tests

- [Unit tests should be short and simple](unit-testing/tests-should-be-short-and-simple.md)
- [Use test cases appropriately](unit-testing/use-test-cases-appropriately.md)
- [Keep app functions pure if possible](unit-testing/keep-functions-pure-if-possible.md)
- [Be wary of refactoring tests](unit-testing/be-wary-of-refactoring-tests.md)
- [Prefer black box over white box testing](unit-testing/prefer-black-box-over-white-box-testing.md)
- [Only test the public interface](unit-testing/only-test-the-public-interface.md)
- [Create reusable concrete mocks](unit-testing/create-reusable-concrete-mocks.md)
- [Consider using AutoFixture](unit-testing/consider-using-autofixture.md)
- [Further reading](https://github.com/jpreese/CSharpUnitTestingBestPractices)

#### GUI and E2E tests

- [Use a Page Object to interact with your app](gui-testing/page-object-model.md)
- [Use the `data-selenium` attribute to identify your elements](gui-testing/data-selenium.md)
- [Use CSS-selectors instead of XPath to access elements](gui-testing/css-selectors.md)

## Collections

#### Choosing the correct collection type 
- [Choosing the correct type for an enumerable](collections/choosing-collection-implementation.md)
- [Collections on public APIs](collections/public-api-types.md)
- [Collections on non-public API methods](collections/non-public-api-types.md)
- [Do not use Arrays, except...](collections/arrays.md)
- [Queues](collections/queues.md)
- [IQueryable](collections/iqueryable.md)
- [Immutable collections](collections/immutable.md)
- [Using reference types as keys](collections/reference-types-as-keys.md)

#### General
- [Do not return null to represent an empty enumerable](collections/null-empty-enumerables.md)
- [Do not check for presence of elements before enumerating a collection](collections/dont-check-collection-size.md)

## Asynchronous programming
- [Avoid blocking when possible](async/avoid-blocking.md)
- [Always use the asynchronous version of a method when it exists](async/consume-async-method.md)
- [Do not expose both sync and async versions of methods](async/expose-async-method.md)
- [Avoid mixing synchronous and asynchronous code](async/avoid-mixing-async-sync.md)
- [Return `Task` instead of `void` for async methods with no return value](async/do-not-return-void.md)
- [Use `await task` instead of `task.Result`](async/await-task-result.md)
- [Never use `Task.Wait`](async/never-task-wait.md)
- [Use `await Task.WhenAny/All` instead of `Task.WaitAny/All`](async/when-any-all.md)
- [Prefer to start tasks with `Task.Run`](async/task-run.md)
- [Use `await` instead of `Task.ContinueWith`](async/never-task-continue-with.md)
- [Avoid unnecessary use of `async` modifier](async/avoid-unnecessary-async.md)
- [Avoid race conditions with proper synchronization](async/avoid-race-conditions.md)
- [Correct use of `ConfigureAwait(false)`](async/configure-await.md)

## Reflection
- [Do not use hard coded string to identify types](reflection/hard-coded-strings.md)

## Code style
- [Naming conventions](code-style/naming-conventions.md)
- [Do not (explicitly) use monads](code-style/monads.md)
- [Do not use `dynamic`](code-style/dynamics.md)
- [Do not use `#region`](code-style/regions.md)

## Configuration
- [If it's unlikely to change, don't make it configurable](configuration/unlikely-to-change.md)
- [Using Consul](configuration/consul.md)
- [Use config files only for Consul client settings and sensitive data](configuration/config-files.md)
- [Use Consul's service discovery for server addresses](configuration/service-discovery.md)
- [Use Consul's Key/Value store for anything else](configuration/key-value.md)
- [Do not use MachineName to configure the application](configuration/machine-name.md)

## Strings
- [For string operations, always explicitly specify comparison/culture rules](strings/do-not-rely-on-default-rules.md)

## Routing
- [Use attribute-based routing instead of convention-based routing](routing/attribute.md)

## Logging
- [Logging](logging/general.md)
- [Log levels](logging/log-levels.md)

