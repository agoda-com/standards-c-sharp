## Use test cases to test for boundary conditions

`[TestCase]` is great for testing boundary conditions.

### Compliant solution

```c#
[Test]
[TestCase(-1, false)]
[TestCase(0, false)]
[TestCase(1, true)]
[TestCase(10, true)]
[TestCase(11, false)]
public void NumberIsAPositiveIntegerLessThanOrEqualTo10_DeterminesCorrectly(int number, bool expectedResult)
{
    // ...
}
``` 