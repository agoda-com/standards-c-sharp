## Tests should be short and simple

- Avoid "god" tests where everything about a class is tested in a single method.
- Keep them simple and focused, testing one requirement a time - a short story, not an epic novel.
- These kind of tests are much easier to fix if they break.
- If there are very many asserts then that may be an indication you are testing too much in one go.

### Noncompliant code

```c#
[TestFixture()]
public class PillViewModelBuilderTests
{
    private Mock<IPerksListBreakdownBuilder> _perksListBreakdownBuilder;
    
        private List<FreebiesItem> CreatePillsList()
        {
            return new List<FreebiesItem>()
            {
                new FreebiesItem() { Name = "Apple TV", Icon = "ficon-apple-tv" },
                new FreebiesItem() { Name = "Iphone", Icon = "ficon-iphone" },
                new FreebiesItem() { Name = "Mac", Icon = "ficon-mac" },
                new FreebiesItem() { Name = "Ipad", Icon = "ficon-ipad" },
                new FreebiesItem() { Name = "Apple Watch", Icon = "ficon-apple-watch" }
            };
        }
                
        [Test()]
        [TestCase(2, true, "+3 More")]
        [TestCase(5, false, "+5 More")]
        public void PillViewModelBuilderTest(int perklist, bool expectedShowTeaser, string expectedTeaser)
        {
            var pillsList = CreatePillsList();
        
            _perksListBreakdownBuilder = new Mock<IPerksListBreakdownBuilder>();
            _perksListBreakdownBuilder.Setup(x => x.CalculateHighlightedIndex(It.IsAny<List<string>>(), It.IsAny<string>(), It.IsAny<string>(), 25)).Returns(perklist);
        
            PillViewModelBuilder pillBuilder = new PillViewModelBuilder(_perksListBreakdownBuilder.Object);
        
            var pillArgs = new PillArgs()
            {
                PillStyle = "green-pill",
                PillTitleText = "Title",
                PillTooltipText = "Tooltip",
                TeaserTemplate = "+{0} More"
            };
        
        
            // Check Pill Title, style and tooltip mapping 
            var pills =  pillBuilder.Build(pillsList, pillArgs);
            Assert.AreEqual("Title", pills.PillText);
            Assert.AreEqual("Tooltip", pills.PillTooltipText);
            Assert.AreEqual(expectedTeaser, pills.PillTeaserText);
            Assert.AreEqual("green-pill", pills.StyleName);
        
            // Check inside Pills list mapping 
            for (int i = 0; i < pillsList.Count; ++i)
            {
                Assert.AreEqual(pillsList[i].Name, pills.PillList[i].Name);
                Assert.AreEqual(pillsList[i].Icon, pills.PillList[i].Icon);
            }
        
            // Check pill breakdown teaser 
            Assert.AreEqual(expectedShowTeaser, pills.ShouldShowTeaser);
        }
    }
}
```

### Compliant solution

```c#
[TestFixture()]
public class PillViewModelBuilderTests
{
    private List<FreebiesItem> _freebiesItems;
    private Mock<IPerksListBreakdownBuilder> _perksListBreakdownBuilder;
    private PillArgs _pillArgs;

    [SetUp]
    public void SetUp()
    {
        _perksListBreakdownBuilder = new Mock<IPerksListBreakdownBuilder>();
        
        _freebiesItems = new List<FreebiesItem>
        {
            new FreebiesItem { Name = "Apple TV", Icon = "ficon-apple-tv" },
            new FreebiesItem { Name = "Iphone", Icon = "ficon-iphone" },
            new FreebiesItem { Name = "Mac", Icon = "ficon-mac" },
            new FreebiesItem { Name = "Ipad", Icon = "ficon-ipad" },
            new FreebiesItem { Name = "Apple Watch", Icon = "ficon-apple-watch" }
        };
        
        _pillArgs = new PillArgs
        {
            PillStyle = "green-pill",
            PillTitleText = "Title",
            PillTooltipText = "Tooltip",
            TeaserTemplate = "+{0} More",
            MaxTooltipItems = Int32.MaxValue
        };
    }

    [Test]
    public void Build_SetsBasicProperties()
    {
        var perksListBreakdownBuilderMock = new Mock<IPerksListBreakdownBuilder>();
        var pillArgs = new PillArgs
        {
            PillStyle = "green-pill",
            PillTitleText = "Title",
            PillTooltipText = "Tooltip",
            TeaserTemplate = "+{0} More"
        };   
        var pillBuilder = new PillViewModelBuilder(perksListBreakdownBuilderMock.Object);

        var pills =  pillBuilder.Build(_freebiesItems, pillArgs);
        
        Assert.AreEqual("Title", pills.PillText);
        Assert.AreEqual("Tooltip", pills.PillTooltipText);
        Assert.AreEqual("green-pill", pills.StyleName);
    }

    [Test]
    [TestCase(0, "+5 More")]
    [TestCase(1, "+4 More")]
    public void Build_SetsCorrectTeaserText(int highlightedIndex, string expectedTeaser)
    {
        _perksListBreakdownBuilder
            .Setup(x => x.CalculateHighlightedIndex(
                It.IsAny<List<string>>(), 
                It.IsAny<string>(), 
                It.IsAny<string>(),
                It.IsAny<int>()))
            .Returns(highlightedIndex);
        var pillBuilder = new PillViewModelBuilder(_perksListBreakdownBuilder.Object);
        
        var pills =  pillBuilder.Build(_freebiesItems, _pillArgs);
        
        Assert.AreEqual(expectedTeaser, pills.PillTeaserText);
    }

    [Test]
    [TestCase(5, false)]
    [TestCase(4, true)]
    [TestCase(0, true)]
    public void Build_SetsShowTeaserFlag(int highlightedIndex, bool expectedShowTeaser)
    {
        _perksListBreakdownBuilder
            .Setup(x => x.CalculateHighlightedIndex(
                It.IsAny<List<string>>(), 
                It.IsAny<string>(), 
                It.IsAny<string>(),
                It.IsAny<int>()))
            .Returns(highlightedIndex);
        var pillBuilder = new PillViewModelBuilder(_perksListBreakdownBuilder.Object);
        
        var pills =  pillBuilder.Build(_freebiesItems, _pillArgs);
        
        Assert.AreEqual(expectedShowTeaser, pills.ShouldShowTeaser);
    }
    
    [Test]
    public void Build_MapsFreebiesToPillList()
    {
        var pillBuilder = new PillViewModelBuilder(_perksListBreakdownBuilder.Object);
        
        var pills =  pillBuilder.Build(_freebiesItems, _pillArgs);

        for (int i = 0; i < _freebiesItems.Count; ++i)
        {
            Assert.AreEqual(_freebiesItems[i].Name, pills.PillList[i].Name);
            Assert.AreEqual(_freebiesItems[i].Icon, pills.PillList[i].Icon);
        }
    }

    [Test]
    [TestCase(0, 0)]
    [TestCase(4, 4)]
    [TestCase(5, 5)]
    [TestCase(6, 5)]
    public void Build_PillListLengthCappedAtMaxTooltipItems(int maxItems, int expectedListLength)
    {
        var pillBuilder = new PillViewModelBuilder(_perksListBreakdownBuilder.Object);
        _pillArgs.MaxTooltipItems = maxItems;
        
        var pills =  pillBuilder.Build(_freebiesItems, _pillArgs);

        Assert.AreEqual(expectedListLength, pills.PillList.Count);
    }
}

```