## Use test cases appropriately

Overuse of `[TestCase]` can make it difficult to see what is actually being tested, especially when there are many parameters. This can results in a combinatorial explosion of cases as new parameters are added.

### Noncompliant code

Here it's difficult from the test name and cases to determine what we are actually testing.

```c#
 [Test]
[TestCase(ProductType.AllRooms, 0, true)]
[TestCase(ProductType.Hotels, 0, true)]
[TestCase(ProductType.Homes, 0, true)]
[TestCase(ProductType.AllRooms, 1, false)]
[TestCase(ProductType.Hotels, 1, false)]
[TestCase(ProductType.Homes, 1, true)]
public void ShouldNotRequestSecretDealForAgodaHomesTab(ProductType productType, int additionalFilterCount, bool apoRequestShouldBeNull)
{
    var pageSizeSearchProviderMock = MockIPageSizeSearchProvider(10);
    var apoReqBuilder = new ApoRequestBuilder(pageSizeSearchProviderMock.Object);

    var mockCriteria = new MockSearchCriteriaBuilder().Build();
    mockCriteria.ProductType = productType;
    mockCriteria.Filters = new SearchFilterCriteria
    {
        ProductType = new[] { (int) productType },
        Facilities = Enumerable.Range(0, additionalFilterCount).ToArray()
    };

    var mockExperimentManager = new Mock<IExperimentManager>();
    mockExperimentManager.Setup(x => x.PreDetermineVariant(ExpId.NHA_PRODUCT_TYPE_TABS_ON_SSR)).Returns('B');

    var baseSearchReqDtoMock = MockBaseRequestReqDto(false);
    baseSearchReqDtoMock.SetupGet(x => x.Criteria).Returns(mockCriteria);
    baseSearchReqDtoMock.SetupGet(x => x.ExperimentManager).Returns(mockExperimentManager.Object);

    var apoRequest = apoReqBuilder.BuildRequest(baseSearchReqDtoMock.Object);

    Assert.AreEqual(apoRequestShouldBeNull, apoRequest == null, "APO result should be null for ProductType tab: " + apoRequestShouldBeNull);            
}
```

### Compliant solution

By splitting the test into two and naming appropriately, the intention becomes more obvious.

```c#
[Test]
[TestCase(ProductType.AllRooms)]
[TestCase(ProductType.Hotels)]
[TestCase(ProductType.Homes)]
public void Build_WithoutAdditionalFilters_ShouldNotRequestApo(ProductType productType)
{
    var pageSizeSearchProviderMock = MockIPageSizeSearchProvider(10);
    var apoReqBuilder = new ApoRequestBuilder(pageSizeSearchProviderMock.Object);

    var mockCriteria = new MockSearchCriteriaBuilder().Build();
    mockCriteria.ProductType = productType;
    mockCriteria.Filters = new SearchFilterCriteria
    {
        ProductType = new[] { (int) productType },
    };

    var mockExperimentManager = new Mock<IExperimentManager>();
    mockExperimentManager.Setup(x => x.PreDetermineVariant(ExpId.NHA_PRODUCT_TYPE_TABS_ON_SSR)).Returns('B');

    var baseSearchReqDtoMock = MockBaseRequestReqDto(false);
    baseSearchReqDtoMock.SetupGet(x => x.Criteria).Returns(mockCriteria);
    baseSearchReqDtoMock.SetupGet(x => x.ExperimentManager).Returns(mockExperimentManager.Object);

    var apoRequest = apoReqBuilder.BuildRequest(baseSearchReqDtoMock.Object);

    Assert.IsNull(apoRequest);            
}

[Test]
[TestCase(ProductType.AllRooms, false)]
[TestCase(ProductType.Hotels, false)]
[TestCase(ProductType.Homes, true)]
public void Build_WithAdditionalFilters_ShouldRequestApoExceptForHomesProductType(
    ProductType productType,
    bool apoRequestShouldBeNull)
{
    var pageSizeSearchProviderMock = MockIPageSizeSearchProvider(10);
    var apoReqBuilder = new ApoRequestBuilder(pageSizeSearchProviderMock.Object);

    var mockCriteria = new MockSearchCriteriaBuilder().Build();
    mockCriteria.ProductType = productType;
    mockCriteria.Filters = new SearchFilterCriteria
    {
        ProductType = new[] { (int) productType },
        Facilities = new[] { 1 }
    };

    var mockExperimentManager = new Mock<IExperimentManager>();
    mockExperimentManager.Setup(x => x.PreDetermineVariant(ExpId.NHA_PRODUCT_TYPE_TABS_ON_SSR)).Returns('B');

    var baseSearchReqDtoMock = MockBaseRequestReqDto(false);
    baseSearchReqDtoMock.SetupGet(x => x.Criteria).Returns(mockCriteria);
    baseSearchReqDtoMock.SetupGet(x => x.ExperimentManager).Returns(mockExperimentManager.Object);

    var apoRequest = apoReqBuilder.BuildRequest(baseSearchReqDtoMock.Object);

    Assert.AreEqual(apoRequestShouldBeNull, apoRequest == null);            
}
```