## Tests should be written as if they are a specification

- Think of the tests as the _specification_ for your application.
- We should be able to tell exactly how a method should behave just by looking at the names (and perhaps the test cases) of its tests. No need to even look at the code, either test or implementation.
- Create one or more tests for each granular requirement.
- Writing tests as spec gives us documentation _for free_. And, unlike Confluence or Word documents, this documentation **can never go out of date**!

### Don't

```c#
[Test]
public void CalculatorWorks()
{
    var calculator = new Calculator();
    Assert.AreEqual(3, calculator.Add(1, 2));
    Assert.AreEqual(6, calculator.Multiply(3, 2));
}
```

### Do

```c#
[Test]
public void Add_CorrectlyAddsTwoNumbers()
{
    var calculator = new Calculator();
    var result = calculator.Add(1, 2);
    Assert.AreEqual(3, result);
}

[Test]
public void Multiply_CorrectlyMultipliesTwoNumbers()
{
    var calculator = new Calculator();
    var result = calculator.Multiply(3, 2);
    Assert.AreEqual(6, result);
}
```