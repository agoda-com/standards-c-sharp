## Usage of `IQueryable<T>`

### In WebApi

Return [`IQueryable<T>` from a WebApi endpoint](https://docs.microsoft.com/en-us/aspnet/web-api/overview/odata-support-in-aspnet-web-api/supporting-odata-query-options) when you want to allow artitrary server-side filtering and paging. For example, to allow the client to arbitrarily query the products database:

```c#
public IQueryable<Product> Get() 
{
    return allProducts.AsQueryable();
}
```

The client can then construct arbitrary queries such as:

- `http://localhost/odata/Products?$orderby=Category,Price desc`
- `http://localhost/Products?$filter=substringof('zz',Name)`

### In a class library

Return `IQueryable<T>` from a class library when all of the following are true:

- You want a to expose a dataset that can be queried arbitrarily.
- The dataset is not already materialized in memory (just return `IEnumerable<T>`).
- You can take advantage of the provided expression tree to optimize retrieval of the data.

An example is a LINQ to SQL provider. The C# expression tree provided by the consumer is translated into actual SQL statements that can be executed by the target database. Note: this is a dark art and not for the faint-hearted.