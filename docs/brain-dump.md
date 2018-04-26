## Philosophy: KISS

Obligatory trite quotes:

_Keep It Simple, Stupid_ - Some guy

_Everything should be made as simple as possible, but no simpler_ - Albert Einstein

## Design

### Interfaces

- Only create an interface if at least one of the following is true:
  1. Multiple implementations exist in application code.
  2. The component needs to be mocked for testability.

Unnecessary interfaces pollute the codebase and make it more difficult to understand.
  
### Injection and mocking

- A component only needs to be mocked if:
  1. it is _impure_, ie:
    - it has a side effect such as making an external call (eg. reading from disk, making an HTTP call)
    - it is non-deterministic - that is, it may yield different outputs when called multiple times with the same inputs (eg. `DateTime.Now`).
  2. it is responsible for creating a component described by 1 (eg. a factory).
- An impure/non-deterministic component is a good candidate for dependency injection. This promotes testability and keeps components loosely coupled.
- A pure/deterministic component or function operates in-memory and doesn't depend on external resources such as network calls, reading from hard disk etc. It therefore doesn't need to be mocked, swapped out, or injected. Prefer instead to create and access them as a static method, or just `new` up your class inline.

### Dependencies

- Keep each component's dependency count to a reasonable level. The more dependencies, the more difficult the code becomes to change. Let's say... 7?
- Declare only impure services as dependencies (see above). This helps reduce the dependency count, and Keeps It Simple.
- If a component's dependency count gets out of control this is a good indication that it should be refactored into more narrowly focused responsibilities.
- Declare a component's dependencies explicitly in its constructor. Don't use property injection. It's [super lame](http://simpleinjector.readthedocs.io/en/latest/advanced.html#property-injection).
- Each injectable component should expose only a [single public constructor](https://www.cuttingedge.it/blogs/steven/pivot/entry.php?id=97). Multiple constructors lead to a fragile design and present maintainability issues.
- Register dependencies with the container by decorating the class with a suitable attribute. (Done in YCS but not yet implemented in Dictator I believe).
- **For the love of god do not use `ServiceLocator.Current`.** Inject your dependency through the constructor.

### Example

Say we have a model that needs to be mapped to a view model:

```c#
public class MyModel
{
    public int Id { get; set; }
}

public class MyViewModel
{
    public int ModelId { get; set; }
}
```

#### Don't

```c#
// so interface, much dependencies...

interface IViewModelMapper<TFrom, TTo>
{
    TTo Map(TFrom from);
}

public class MyViewModelMapper : IViewModelMapper<MyModel, MyViewModel>
{
    public MyViewModel Map(MyModel model)
    {
        return new MyViewModel
        {
            ModelId = model.Id
        };
    }
}

public class MyController
{
    private MyViewModelMapper mapper;
    private IMyService service;
    
    // Unnecessarily injecting a pure service.
    public MyService(MyViewModelMapper mapper, IMyService service)
    {
        this.mapper = mapper;
        this.service = service;
    }
    
    public MyViewModel Get()
    {
        var model = service.GetModel();
        var viewModel = mapper.Map(model);
        return Json(viewModel);
    }
```

#### Do

```c#
// KISS

public class MyViewModel
{
    public int Id { get; set; }

    // Method is pure, doesn't need to be mocked, so just make it static:
    public static MyViewModel From(MyModel model)
    {
        return new MyViewModel
        {
            ModelId = model.Id
        };
    }      
}

public class MyController : ApiController
{
    private IMyService service;
    
    // We removed an unnecessary dependency!
    public MyService(IMyService service)
    {
        this.service = service;
    }
    
    public MyViewModel Get()
    {
        var model = service.GetModel();
        var viewModel = MyViewModel.From(model);
        return Json(viewModel);
    }
}
```

### Abstractions

- Do not depend of framework abstractions (like `HttpContext`). They tie your code to a specific environment / implementation. Instead, wrap in an abstraction suitable for your application.
- Consider the [Law (Suggestion) of Demeter](https://hackernoon.com/object-oriented-tricks-2-law-of-demeter-4ecc9becad85); that is, avoid long chains of accessors such as `a.b().c.d`. They tightly couple your code to the outside world, making it more difficult to change or test.


#### Don't

```c#
public sealed class CustomerRepository : ICustomerRepository
{
    private readonly IHttpContextAccessor accessor;
    private readonly IUnitOfWork uow;

    public CustomerRepository(IUserContext userContext, IUnitOfWork uow)
    {
        this.userContext = userContext;
        this.uow = uow;
    }

    public void Save(Customer entity)
    {
        // Not only do we tie ourselves to the ASP.NET Core MVC environment,
        // but our class becomes annoying to test as we have to mock this
        // long chain of objects:
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
    string Name { get; }
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
    
    public string Name => accessor.HttpContext.Context.User.Identity.Name;
}

public sealed class CustomerRepository : ICustomerRepository
{
    private readonly IUserContext userContext;
    private readonly IUnitOfWork uow;

    // We configure the container to inject our env-specific abstraction.
    public CustomerRepository(IUserContext userContext, IUnitOfWork uow)
    {
        this.userContext = userContext;
        this.uow = uow;
    }

    public void Save(Customer entity)
    {
        // Now, we've isolate ourselves from the underlying framework, allowing our
        // code to also be run from a Windows service, a console application, etc.
        // Further, we've eliminated the trainwreck above, so our class becomes much more
        // straightforward to test.
        entity.CreatedBy = userContext.Name;
        uow.Save(entity);
    }
}
```
    
## nulls

_"The billion dollar mistake"_

Avoid nulls where possible, because they:

- DO BAD SHIT 
- And also [much better reasons](https://www.lucidchart.com/techblog/2015/08/31/the-worst-mistake-of-computer-science/) (seriously, read it!)

Consider using an Option/Maybe type to represent the potential absence of a value. 


#### Don't

```c#
public List<int> GetPropertyIds(int hostId)
{
    var properties = propertyService.GetPropertiesForHost(hostId);
    
    if (properties == null || !properties.Any()) 
    {
        return null; 
        // NO! Now the caller has to somehow know to deal with this special case.
        // You are asking for a NullReferenceException in prod. Hope you like 
        // 3am wakeup calls!
    }
    
    return properties.Select(p => p.Id).ToList();
}
```

#### Do

```c#
public List<int> GetPropertyIds(int hostId)
{
    var properties = propertyService.GetPropertiesForHost(hostId);
    
    if (properties == null) 
    {
        // Just return an empty List and everything should just work.
        return Enumerable.Empty<int>().ToList();
    }
    
    return properties.Select(p => p.Id).ToList();
}
```

#### Even better

```c#
public List<int> GetPropertyIds(int hostId)
{
    // Fix propertyService.GetProperties() to never return null itself, and the
    // code becomes even more elegant, BAM!
    var properties = propertyService
        .GetPropertiesForHost(hostId)
        .Select(p => p.Id)
        .ToList();
}
```

## Parallelism

### CPU bound

_Avoid unbounded CPU-bound parallelism._

- In fact, avoid parallelism in general unless you know FOR SURE (ie. you have measured) that it will _significantly_ improve performance, _and_ performance is critical.
- If in doubt, serialize it.

#### Don't

```c#
var universe = multiverseService.GetUniverseById(42); // this is ours
Parallel.ForEach(
    universe.Galaxies, 
    // goodbye web server
    galaxy => Console.WriteLine(galaxy.CountParticles())
);
```

#### Do


```c#
var universe = multiverseService.GetUniverseById(42);
Parallel.ForEach(
    universe.Galaxies, 
    new ParallelOptions { MaxDegreeOfParallelism = 4 }, // cores
    // the universe may have reached heat-death by the time this completes,
    // but at least agoda.com will still be up
    galaxy => Console.WriteLine(galaxy.CountParticles())
);
```

### Asyncronous

- Unbounded parallelism may be desirable for asynchronous IO, where threads are not tied up for lengthy periods:

#### Do

```c#
var tasks = notificationService
    .GetPendingNotifications()
    .Select(n => notificationService.SendNotification(n));
await Task.WhenAll(tasks);
```
