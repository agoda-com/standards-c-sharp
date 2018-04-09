## Consider using AutoFixture

Autofixture will automatically create test data. Consider using it to shorten your tests and save your brain power thinking up random data.

https://github.com/AutoFixture/AutoFixture

### Don't

```c#
[Test]
public void CreateViewModel_FormatsLocation()
{
    var cityId = 1;
    var cityName = "Bangkok";
    var countryName = "Thailand";
    var expectedLocation = "Isn't this so boring making up test data?"
    
    // ...
    
    Assert.AreEqual($"{cityName}, {countryName}", result.Location);
}
```

### Do

```c#
[Test]
[AutoData]
public void CreateViewModel_FormatsLocation(int cityId, string cityName, string countryName, string expectedLocation)
{
    // now cityId, cityName, countryName and expectedLocation are automatically set to some random data
    
    // ...
    
    Assert.AreEqual($"{cityName}, {countryName}", result.Location);
}
```