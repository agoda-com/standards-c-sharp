## Prefer black box over white box testing

- **Black box**: we cannot "see" inside the code being tested - we consider only its input and output. What happens in  between is and magic that we don't need to understand. This makes refactoring easy, as we are free to change the implementation ("what's inside the box"), as long as the output stays the same.
 - **White box**: our test depends upon the implementation, for instance by verifying that a certain method was called. Classes such as directors or orchestrators, which serve only to call other methods, might be good candidates for white box testing, but use only as a last resort. White box tests are brittle and therefore harder to maintain.
 
 Consider the following code:
 
 ```c#
 public interface IDataProvider
 {
     string GetData();
 }
 
public class Sleepy
{
    private readonly IDataProvider _dataProvider;

    public Sleepy(IDataProvider dataProvider)
    {
        _dataProvider = dataProvider;
    }

    public string GetSleepyData()
    {
        return _dataProvider.GetData() + " zzzzzzz";
    }
}
 ```
 
### Don't
 
Notice the call to `dataProviderMock.Verify()`.
 
```c#
[Test]
public void GetSleepyData_MakesSleepy()
{
    var dataProviderMock = new Mock<IDataProvider>();
    dataProviderMock.Setup(x => x.GetData()).Returns("I'm tired");
    var sleepy = new Sleepy(dataProviderMock.Object);

    var result = sleepy.GetSleepyData();
    
    Assert.AreEqual("I'm tired zzzzzzz", result);
    dataProviderMock.Verify(x => x.GetData(), Times.Once);
}
```
  
### Do
 
For this case there is no need to verify the call to the data provider was made. This is an implementation detail that might change in the future, and could therefore cause the test to fail. All we care is that the output is correct for the given input - ie. the zeds were appended. We don't care _how_ this was achieved.
 
 ```c#
[Test]
public void GetSleepyData_MakesSleepy()
{
    var dataProviderMock = new Mock<IDataProvider>();
    dataProviderMock.Setup(x => x.GetData()).Returns("I'm tired");
    var sleepy = new Sleepy(dataProviderMock.Object);

    var result = sleepy.GetSleepyData();
     
    Assert.AreEqual("I'm tired zzzzzzz", result);
}
 ```
