## Only test the public interface

- This relates to [Prefer black box over white box testing](prefer-black-box-over-white-box-testing.md). By testing the internals of a class, your tests become brittle as you are not only testing the _result_ but also the _implementation_.
- When testing implementation, in becomes set in stone, so your class becomes more difficult to refactor without also changing tests. This is the opposite of what we want to achieve.
- When refactoring, the best case scenario is: make changes -> run tests -> everything still passes. This becomes much less likely if internals are being tested.
- If a class is difficult to test through its public interface, then this may be a code smell that the class is poorly designed. If _you_ as the author are having trouble using your class, think about your poor consumers.
- **Never** make a method public just so it can be tested. Each time you do this **multiple** kittens die.
- Do not use the [`InternalsVisibleToAttribute`](https://docs.microsoft.com/en-us/dotnet/api/system.runtime.compilerservices.internalsvisibletoattribute?view=netframework-4.7.1) for the aforementioned reasons. Won't someone think of the kittens?

Consider the following interface:

```c#
public interface IDogTranslator
{
    string ToDog(string input);
}
```

### Don't

Here, we see the `RepeatWord` function has been made public and tested separately.

```c#    
public class DogTranslator : IDogTranslator
{
    public string ToDog(string input)
    {
        return $"{input} {RepeatWord("woof", 3)}";
    }
    
    public static string RepeatWord(string word, int times)
    {
        return String.Join(" ", Enumerable.Repeat(word, times));
    }
}

[TestFixture]
public class DogTranslatorTest
{
    [Test]
    public void TranslateToDog_Woofs()
    {
        var translator = new DogTranslator();
        var result = translator.ToDog("I'm a dog");
        Assert.AreEqual("I'm a dog woof woof woof", result);
    }
    
    [Test]
    public void RepeatWord_Repeats()
    {
        var result = DogTranslator.RepeatWord("meow", 3);
        Assert.AreEqual("meow meow meow", result);
    }
}
```

### Do

`RepeatWord` is not part of the `IDogTranslator` interface. It exists only as an implementation detail. We should make it private it and remove its test. It will still be tested, but by calling through the public interface. We are now free to change the implementation without breaking the test.

```c#
public class DogTranslator : IDogTranslator
{
    public string ToDog(string input)
    {
        return $"{input} {RepeatWord("woof", 3)}";
    }
    
    private static string RepeatWord(string word, int times)
    {
        return String.Join(" ", Enumerable.Repeat(word, times));
    }
}

[TestFixture]
public class DogTranslatorTest
{
    [Test]
    public void TranslateToDog_Woofs()
    {
        var translator = new DogTranslator();
        var result = translator.ToDog("I'm a dog");
        Assert.AreEqual("I'm a dog woof woof woof", result);
    }
}
```