## Test method names should clearly indicate what they are testing

- A test's purpose (its pre- and post-conditions) should be obvious from its name alone. It should not be necessary to read the code to work out what is being tested.
- A broken test is much easier to fix when it is obvious what is being tested and therefore what must have broken.
- This is strongly related to [tests as a specification](tests-should-be-written-as-if-they-are-a-specification.md).

Therefore, names tests in one of the following formats:

```c#
// seperated by underscores
public void <MethodUnderTest>_<PostCondition>()
{...}

// or
public void <MethodUnderTest>_<PreCondition>_<PostCondition>()
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

If you are having difficutly naming your test this way, it might indicate it is doing too much and should be split into more [granular tests](tests-should-be-short-and-simple.md).