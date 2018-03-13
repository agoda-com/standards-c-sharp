These are the C# coding standards and practices for new (non-legacy) code. This is a living document. Please discuss on #code-quality and send a pull request if you would like to contribute.

This document is currently a brain dump and will be broken up into focussed issues as we progress.

## Philosophy: KISS

_Keep It Simple, Stupid_ - Some guy

_Everything should be made as simple as possible, but no simpler_ - Albert Einstein

## Folder structure

_Organize primarily by what it does, not what it is._

```
+- MyProject
   +- Functional Area 1 (eg. Geography)
      +- Controllers
      +- Repositories
      +- Services
      +- ViewModels
   +- Functional Area 2 (eg. Search)
      +- Controllers
      +- Repositories
      +- Services
      +- ViewModels
   etc.
```

Split functional areas into subfolders as/when they grow.

Consider whether "DTO" is a better name than "ViewModel".

Consider if your team name really represents a functional area, especially if you work in a horizontal.

## Architecture

- We follow a tiered architecture where the data flows Repo <=> Service <=> Controller <=> View Model / DTO.
- Consider omitting the Service layer if it only serves as a trivial wrapper around the Repository. This reduces boilerplate and accelerates development. Yes, this means your controller can call a repository directly. Introduce the Service layer later once it becomes necessary.
    - This is controversial. Should we try it and see how it works out?
- The ViewModel / DTO layer must never be skipped (ie. do not serialize Service or Repository objects directly to the client). This isolates the client from changes to the backend, and prevents new and potentially sensitive fields from being inadvertently exposed. 

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
- Declare a component's dependencies explicitly in its constructor. Don't use property injection. It's super lame.
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

## Errors and exceptions

_Fail as quickly and as loudly as possible._

We can only fix the problems we know about. Therefore, don't be tempted to hide exceptions from the user simply to "improve" the UX. This just leads to long-term difficult-to-diagnose inconsistencies and weirdness.

### Catching exceptions

_Only catch what you can handle._

- Do not _catch_ an exception unless you have a good reason to do so. Such reasons might be:
   - recovering from the error
   - enriching the error message / wrapping the exception
   - retrying the operation
   - logging the error
- There should always be a global exception handler that logs any unhandled exceptions and shows a "sorry, something went wrong" message. If all you want to do is log the error, then don't bother - keep your code clean and leave the logging to the global exception handler.
- When catching an exception, be as specific as possible to the type of exception you are handling. Avoid catching `System.Exception` if you only plan to handle a `System.IO.FileNotFoundException`.

### Consuming exceptions

_Don't. Unless you know what you are doing. But even then, probably don't._

- When you catch an exception but don't rethrow it, you have _consumed_ the exception. As far as the rest of the system is concerned, the exception never happened.
- To consume an exception implies that you have completely recovered from the error, and the user can continue as if nothing ever happened.
- Read that last sentence again and consider its implications. It is actually quite rare to be able to gracefully recover from an exception, because the circumstances should be: _exceptional_. In other words, as the programmer, it is unlikely that you can foresee and mitigate every little thing that can possibly go wrong. And this is OK! See _Rethrowing exceptions_ below.
- Do not consume an exception unless you have actively taken steps to correct the problem and bring the system back into a consistent state. The hardest exceptions to troubleshoot are the ones that don't even exist, because someone upstream consumed it.
- For example, if an exception is thrown when writing to a readonly file, then you can recover by removing the readonly attribute, consuming the exception, then retrying. In this case, the user doesn't need to know that anything went wrong, because you were able to completely recover from error condition.
- However, if a service call failed because a backend system was down, then it is very difficult to gracefully recover.
    - Either: pretend to the user that nothing happened? I guess we could present them with a confusing or inconsistent view, with a bunch of fields blanked out?
    - Or: is it better to be explicit: "Really sorry, but something went wrong. Please try again later."
- We admittedly trade off development resources against the likelihood of an error occurring. But, if the system is down for you, it's also down for the rest of the business.
    - CAPI is down for your page and you can't retrieve the user's points balance. But more importantly, we also can't take any bookings AT ALL. So for the business, exactly how much effort is it worth your code gracefully handling this case, when we're already dead in the water, and losing hella-dollars per minute?

### Rethrowing exceptions

_It's not your problem._

- If you cannot recover from an exception, _it's totally fine_! Exceptions should be, by nature, _exceptional_, so as a programmer you can't be expected to mitigate every potential corner case. Someone forgot to deploy the config file? The network went down? The datacenter was destroyed by a hurricane? Christopher Hitchens rose again? _It's not your problem._
  - Either: don't catch the exception in the first place.
  - Else: catch, handle (log?) and _rethrow_.
  - Finally: give someone further up the callstack the chance to handle it more appropriately.
- When _rethrowing_ an exception simply `throw;` it. Do not `throw ex;` as this loses the original call stack information, making it look like the exception originated inside your `catch` block.
- **Note that simply logging an exception does not count as graceful recovery. If in doubt, always rethrow.**

#### Don't

```c#
try
{
    return myService.GetData();
}
catch (Exception ex)
{
    Log.Error(ex.ToString());
    return null;
    // Now the caller has to deal with a myserious null value, with no 
    // indication that something went wrong.
}

```

#### Do

```c#
try
{
    return myService.GetData();
}
catch (MyServiceException ex)
{
    Log.Error(ex.ToString());
    throw; // note: *NOT* throw ex;
    // This time, the caller is notified that something went wrong, and can take 
    // steps to recover, or allow the exception to propagate up the stack. 
}

```

#### Even better

```c#
// Just leave the logging to the global exception handler.
return myService.GetData();
````
    
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