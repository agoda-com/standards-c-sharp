## Keep functions pure if possible

- A Pure function is a function that always returns the same value given the same input, and has no side-effects. It is therefore _isolated_ from the outside world - it depends only on its arguments.
- In unit tests, we try to _isolate_ the code under test from the rest of the system. So if your function is already pure / isolated, it is instrinsically testable without any extra design work.
- The below example is extremely contrived, but think about how this scales up to more complex cases. Keeping our functions pure where possible can simplify our code and make it easier to understand.

### Noncompliant code

Here the `Multiply()` function depends on another property. We require an instance of the class, and to set up its property before we can use it.  And this is reflected in the test.

```c#
public class SillyMultiplier
{
    public decimal Multiplier { get; set; }
    
    public decimal Multiply(decimal number)
    {
        return number * Multiplier;
    }
}

[Test]
public void Multiply_WithMultiplierSet_ReturnsArgumentMultipliedByMultiplier()
{
    var m = new SillyMultiplier { Multiplier = 5 };
    var result = m.Multiply(10);
    Assert.AreEqual(50, result);
}
```

### Compliant solution

Here, because the function is pure, we don't need to even create an instance of the class or set anything up to test it.

```c#
public static class SlightlyLessSillyMultiplier
{
    public static decimal Multiply(decimal number1, decimal number2)
    {
        return number1 * number2;
    }
}

[Test]
public void Multiply_MultipliesTwoNumbersCorrectly()
{
    var result = SlightlyLessSillyMultiplier.Multiply(5, 10);
    Assert.AreEqual(50, result);
}
```