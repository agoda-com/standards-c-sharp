## Be wary of refactoring unit tests

- No one doubts the benefits of refactoring code. It reduces duplication and hopefully makes the code clearer and easier maintain.
 - Unit tests serve a different purpose from production code. They are primarily there to:
   1. ensure your code does what you think it does
   2. enables you to change your code with confidence
 - It's this second point that is the most important here. As we refactor our production code, we rely on our unit tests to make sure we haven't broken anything.
 - When we break a test, we want to be able to diagnose and fix the problem as quickly as possible.
 - If the test is made up a bunch of complex abstractions, like...
   - a common `[BaseTest]` class that does a bunch of magic
   - a factory function that constructs your system under test but takes 11 parameters to cover all possible cases
   - a seperate function that performs the Asserts into which your result is passed, that also takes a bunch of parameters
   
 ...then the test code might be completely DRY, but unless you wrote it all yourself, it's probably going to be difficult to understand.
 
 - Don't be afraid to copy and paste when writing tests if you can keep them reasonably short. Tests should (hopefully) rarely change, so the copied code shouldn't present much of maintenance problem.
 - **Above all, aim for simplicity and readability over DRY and abstracted.**
 
 Consider this stupid class that doesn't really do anything interesting.
 
 ```c#
 public class MyTestClass
 {
     private readonly IExperimentManager _experimentManager;
     public string A { get; set; }
     public string B { get; set; }
     public string C { get; set; }
     public string D { get; set; }
     public string E { get; set; }
     public string F { get; set; }

     public MyTestClass(IExperimentManager experimentManager)
     {
         _experimentManager = experimentManager;
     }

     public void ABC(string a, string b, string c)
     {
         if (_experimentManager.DetermineVariant("EXP-123") != 'A')
         {
             A = a;
             B = b;
             C = c;
         }
     }

     public void DEF(string d, string e, string f)
     {
         D = d;
         E = e;
         F = _experimentManager.DetermineVariant("EXP-123") == 'B' ? $"wt{f}" : f;
     }
 }
 
 ```
 
 ### Noncompliant code
 
 Here the test code is almost completely dry, but quite difficult to read or reason about. There is magic happening in the base class, and we have a `CheckResult` function that takes a bunch of parameters.
 
 ```c#
[TestFixture]
public class BaseTest
{
    protected Mock<IExperimentManager> ExperimentManagerMock;

    [OneTimeSetUp]
    public void OneTimeSetUp()
    {
        ExperimentManagerMock = new Mock<IExperimentManager>();
    }

    protected void SetVariant(string experiment, char variant)
    {
        ExperimentManagerMock.Setup(x => x.DetermineVariant(experiment)).Returns(variant);
    }
}

[TestFixture]
public class MyTestClassTest : BaseTest
{
    private const string ExpId = "EXP-123";
    private MyTestClass _myClass;

    [SetUp]
    public void SetUp()
    {
        _myClass = new MyTestClass(ExperimentManagerMock.Object);
    }
     
    [Test]
    public void ABC_WhenExperimentIsA_AreNotSet()
    {
        SetVariant(ExpId, 'A');
        _myClass.ABC("a", "b", "c");
        CheckResult(_myClass, null, null, null, null, null, null);
    }
 
    [Test]
    public void ABC_WhenExperimentIsB_AreSet()
    {
        SetVariant(ExpId, 'B');
        _myClass.ABC("a", "b", "c");
        CheckResult(_myClass, "a", "b", "c", null, null, null);
    }
     
    [Test]
    public void DEFF_WhenExperimentIsA_Fs()
    {
        SetVariant(ExpId, 'A');
        _myClass.DEF("d", "e", "f");
        CheckResult(_myClass, null, null, null, "d", "e", "f");
    }
     
    [Test]
    public void DEFF_WhenExperimentIsB_Wtfs()
    {
        SetVariant(ExpId, 'B');
        _myClass.DEF("d", "e", "f");
        CheckResult(_myClass, null, null, null, "d", "e", "wtf");
    }

    private void CheckResult(MyTestClass testClass, string expectedA, string expectedB, string expectedC, 
        string expectedD, string expectedE, string expectedF)
    {
        Assert.AreEqual(expectedA, testClass.A);
        Assert.AreEqual(expectedB, testClass.B);
        Assert.AreEqual(expectedC, testClass.C);
        Assert.AreEqual(expectedD, testClass.D);
        Assert.AreEqual(expectedE, testClass.E);
        Assert.AreEqual(expectedF, testClass.F);
    }
}
 ```
 
 ### Compliant solution
 
 Here, the code is more verbose, and there's a bit of copy-pasting, but it's extremely clear, making debugging and fixing easy.
 
 ```c#
 [TestFixture]
 public class MyBetterTestClassTest
 {
     private Mock<IExperimentManager> _experimentManagerMock;

     [SetUp]
     public void SetUp()
     {
         _experimentManagerMock = new Mock<IExperimentManager>();
     }
     
     [Test]
     public void ABC_WhenExperimentIsA_AreNotSet()
     {
         _experimentManagerMock.Setup(x => x.DetermineVariant(It.IsAny<string>())).Returns('A');
         var myClass = new MyTestClass(_experimentManagerMock.Object);
         
         myClass.ABC("a", "b", "c");
         
         Assert.IsNull(myClass.A);
         Assert.IsNull(myClass.B);
         Assert.IsNull(myClass.C);
     }
     
     [Test]
     public void ABC_WhenExperimentIsB_AreSet()
     {
         _experimentManagerMock.Setup(x => x.DetermineVariant(It.IsAny<string>())).Returns('B');
         var myClass = new MyTestClass(_experimentManagerMock.Object);
         
         myClass.ABC("a", "b", "c");
         
         Assert.AreEqual("a", myClass.A);
         Assert.AreEqual("b", myClass.B);
         Assert.AreEqual("c", myClass.C);
     }
     
     [Test]
     public void DEF_WhenExperimentIsA_Fs()
     {
         _experimentManagerMock.Setup(x => x.DetermineVariant(It.IsAny<string>())).Returns('A');
         var myClass = new MyTestClass(_experimentManagerMock.Object);
         
         myClass.DEF("d", "e", "f");
         
         Assert.AreEqual("d", myClass.D);
         Assert.AreEqual("e", myClass.E);
         Assert.AreEqual("f", myClass.F);
     }
     
     [Test]
     public void DEF_WhenExperimentIsB_Wtfs()
     {
         _experimentManagerMock.Setup(x => x.DetermineVariant(It.IsAny<string>())).Returns('B');
         var myClass = new MyTestClass(_experimentManagerMock.Object);
         
         myClass.DEF("d", "e", "f");
         
         Assert.AreEqual("d", myClass.D);
         Assert.AreEqual("e", myClass.E);
         Assert.AreEqual("wtf", myClass.F);
     }
 }
 ```
 
 But there is no hard science to this. Use your best judgement.