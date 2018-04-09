## Test method names should clearly indicate what they are testing

A test's pre- and post-conditions should be obvious from its name, and perhaps its test cases. If it breaks, it should
be obvious what went wrong and how to fix it. Test methods names should be in the format:

```<MethodUnderTest>_<OptionalPreConditions>_<PostCondition>()```

### Don't

```c#
[Test]
public static void HazardLightsTest()
{...}
```

### Do

```c#
[Test]
public static void ToggleHarzardLights_WhenAlreadyBlinking_StopsBlinking()
{...}
```