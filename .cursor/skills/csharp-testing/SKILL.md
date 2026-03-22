---
name: csharp-testing
description: >-
  C# testing philosophy and unit testing best practices. Covers test pyramid,
  test naming conventions, tests as specification, AutoFixture, TestCase usage,
  boundary conditions, test refactoring, mocking, black-box vs white-box
  testing. Use when writing, reviewing, or refactoring C# unit tests,
  integration tests, or test strategies.
---

# C# Testing Standards

## Why we test

- Validate code does what we think it does
- Enable refactoring with confidence
- Ensure bugs stay fixed — always fix a bug by first writing a failing test

## Test naming

Names must clearly indicate what is being tested. Format:

```c#
public class <SystemUnderTest>Tests
{
    [Test]
    public void <Method>_<PreCondition>_<PostCondition>() { ... }
}
```

```c#
// Don't
public void HazardLightsTest() { ... }

// Do
public void HazardLightsToggle_WhenLightsAlreadyBlinking_BlinkingShouldStop() { ... }
```

If naming is difficult, the test may be doing too much — split it.

## Tests as specification

Think of tests as the spec. We should know exactly how a method behaves from test names alone. One or more tests per granular requirement. This is documentation that **can never go out of date**.

```c#
// Don't — one test doing everything
public void CalculatorWorks() { ... }

// Do — separate test per behavior
public void Add_CorrectlyAddsTwoNumbers() { ... }
public void Multiply_CorrectlyMultipliesTwoNumbers() { ... }
```

## Tests should be short and simple

Each test should be easy to read and quick to diagnose when broken. Prefer simple, self-contained tests over complex abstractions.

## Only test the public interface

Don't test private/internal implementation details. Test observable behavior through public methods.

## Prefer black-box over white-box testing

Test what the code does, not how it does it. This makes tests resilient to refactoring.

## Be wary of refactoring tests

Test code priorities differ from production code:

- DRY and abstraction can hurt readability and debuggability
- Avoid: complex base test classes, factory methods with 11 parameters, shared assertion helpers
- Prefer: copy-paste a little, keep tests self-contained and obvious
- **Aim for simplicity and readability over DRY**

## Consider using AutoFixture

Automatically generates test data. Saves time and brain power.

```c#
// Don't — manual boring test data
[Test]
public void CreateViewModel_FormatsLocation()
{
    var cityName = "Bangkok";
    var countryName = "Thailand";
    // ...
}

// Do — auto-generated data
[Test, AutoData]
public void CreateViewModel_FormatsLocation(int cityId, string cityName, string countryName)
{
    // cityId, cityName, countryName auto-populated with random data
}
```

## Use TestCase appropriately

### Avoid overuse

Too many parameters create combinatorial explosions and obscure intent. Split into focused tests.

```c#
// Don't — 3 parameters, unclear intent
[TestCase(ProductType.AllRooms, 0, true)]
[TestCase(ProductType.Hotels, 1, false)]
public void ShouldNotRequestSecretDeal(ProductType pt, int filters, bool shouldBeNull) { ... }

// Do — split by scenario
[TestCase(ProductType.AllRooms)]
[TestCase(ProductType.Hotels)]
public void Build_WithoutAdditionalFilters_ShouldNotRequestApo(ProductType pt) { ... }
```

### Great for boundary conditions

```c#
[TestCase(-1, false)]
[TestCase(0, false)]
[TestCase(1, true)]
[TestCase(10, true)]
[TestCase(11, false)]
public void IsPositiveIntLessThanOrEqualTo10(int number, bool expected) { ... }
```

## Keep functions pure if possible

Pure functions are easier to unit test — no mocking needed. See service design standards for static vs instance guidance.

## Create reusable concrete mocks

When a mock is complex and reused across tests, create a concrete mock class instead of repeating `Mock<T>` setup.
