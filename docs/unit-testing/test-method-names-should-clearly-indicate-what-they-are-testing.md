## Test method names should clearly indicate what they are testing

A test's pre- and post-conditions should be obvious from its name, and perhaps its test cases. If it breaks, it should
be obvious what went wrong and how to fix it. Test methods names should be in the format:

```c#
public void <MethodUnderTest>_<OptionalPreConditions>_<PostCondition>()
{...}
```

### Don't

```c#
[Test]
public void HazardLightsTest()
{...}
```

### Do

```c#
[Test]
public void ToggleHarzardLights_WhenAlreadyBlinking_StopsBlinking()
{...}
```
