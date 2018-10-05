## Do not depend on framework abstractions

- Do not depend of framework abstractions (like `HttpContext`). They tie your code to a specific environment / implementation. Instead, create an environment-specific abstraction.
- Consider the [Law (Suggestion) of Demeter](https://hackernoon.com/object-oriented-tricks-2-law-of-demeter-4ecc9becad85); that is, avoid long chains of accessors such as `a.b().c.d`. They tightly couple your code to the outside world, making it more difficult to change or test.


#### Don't

```c#
public sealed class CustomerRepository : ICustomerRepository
{
    private readonly IHttpContextAccessor accessor;
    private readonly IUnitOfWork uow;

    public CustomerRepository(IHttpContextAccessor accessor, IUnitOfWork uow)
    {
        this.accessor = accessor;
        this.uow = uow;
    }

    public void Save(Customer entity)
    {
        // Not only do we tie ourselves to ASP.NET, but our class becomes annoying 
        // to test as we have to mock this long chain of objects:
        entity.CreatedBy = this.accessor.HttpContext.User.Identity.Name;
        this.uow.Save(entity);
    }
}
```

#### Do

```c#
// Create a non-framework specific abstraction that gives us *only* what we really need.
public interface IUserContext
{
    string Username { get; }
}

// Provide and register an environment specific implementation that encapsulates 
// all the messy details.
public sealed class AspNetUserContext : IUserContext
{   
    private readonly IHttpContextAccessor accessor;
    
    public AspNetUserContext(IHttpContextAccessor accessor) 
    { 
        this.accessor = accessor; 
    }
    
    public string Username => accessor.HttpContext.Context.User.Identity.Name;
}

// Inject the abstraction into our business logic
public sealed class CustomerRepository : ICustomerRepository
{
    private readonly IUserContext userContext;
    private readonly IUnitOfWork uow;

    // We configure the container to inject our application abstraction.
    public CustomerRepository(IUserContext userContext, IUnitOfWork uow)
    {
        this.userContext = userContext;
        this.uow = uow;
    }

    public void Save(Customer entity)
    {
        // Now, we've isolated ourselves from the underlying framework, allowing our
        // code to also be run from a Windows service, a console application, etc.
        // Further, we've eliminated the trainwreck above, so our class becomes much more
        // straightforward to test.
        entity.CreatedBy = userContext.Username;
        uow.Save(entity);
    }
}
```
