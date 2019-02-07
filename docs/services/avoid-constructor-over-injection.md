## Avoid constructor over injection

We have all encountered components with huge lists of constructor parameters. They started with good intentions, but over time more behavior has gradually been added, so the constructor - and indeed the service - have become unwieldy.

This usually indicates two problems:

- For the service to require so many dependencies is almost certainly violating the Single Responsibility Principle.
- Unit testing the component requires setting up huge numbers of mocks.

### Avoid service sets
 
Service sets have been be used to mitigate constructor over injection. For instance, instead of:

#### Don't

```c#
public class MyService : IMyService
{
    public MyService(IDependency1 d1,
        IDependency2 d2,
        IDependency3 d3,
        IDependency4 d4,
        // and 20 more
    )
    {
        _d1 = d1;
        _d2 = d2;
        _d3 = d3;
        _d4 = d4;
        // ... etc
    }
}
```

We create a container class (service set) that takes these dependencies, and inject the container as a single dependency:

#### Still don't

```c#
public class MyServiceSet : IMyServiceSet
{
    public MyServiceSet(IDependency1 d1,
        IDependency2 d2,
        IDependency3 d3,
        IDependency4 d4,
        // and 20 more
    )
    {
        _d1 = d1;
        _d2 = d2;
        _d3 = d3;
        _d4 = d4;
        // ... etc
    }
}

public class MyService : IMyService
{
    public MyService(IMyServiceSet set)
    {
        _set = set;
    }
}
```

But this doesn't really solve anything, and might actually make things worse:

- `MyService` is still doing too much - we've just hidden its ugly constructor inside a new component. Now instead of a service that takes 24 dependencies, we have a service _set_ that does the same. We have solved nothing, hidden the problem, _and_ increased complexity with an extra class/interface.
- It is now not clear from the constructor of `MyService` which particular dependencies from the set it actually requires. We get no help from the tooling, which might otherwise indicate an unused field. We have to dig into the implementation and analyze this for ourselves.

When dealing with constructor over injection, it can be useful to distinguish which dependencies are required _everywhere_ in the service, and which are only required occasionally. A dependency that is required by a single method in a large service indicates that the method (and therefore the dependency it took) might belong somewhere else.

### Refactor to Facade Services

Clearly, the proper solution is to refactor `MyService` into smaller more focused services. Mark Seemann calls these [Facade Services](http://blog.ploeh.dk/2010/02/02/RefactoringtoAggregateServices/):

> While the Facade Service may start its life as a result of a pure mechanistic refactoring, it often turns out that the extracted behavior represents a Domain Concept in its own right.

Seemann has a very low threshold for constructor parameter count, which he says "lies somewhere around 3-4". In his example he starts with 5 constructor arguments:

```c#
public class OrderProcessor
{
    public OrderProcessor(IOrderValidator validator,
       IOrderShipper shipper,
       IAccountsReceivable receivable,
       IRateExchange exchange,
       IUserContext userContext)
    {
       // ...
    }
       
    public SuccessResult Process(Order order)
    {
       bool isValid = _validator.Validate(order);
       if (isValid)
       {
           Collect(order);
           _shipper.Ship(order);
       }
    
       return CreateStatus(isValid);
    }
    
    private void Collect(Order order)
    {
       var user = _userContext.GetCurrentUser();
       var price = order.GetPrice(_exchange, _userContext);
       _receivable.Collect(user, price);
    }
}
```

He comments:

> If you examine the code it should quickly become apparent that the Collect method encapsulates a cluster of dependencies: IAccountsReceivable, IRateExchange and IUserContext. In this case it's pretty obvious because they are already encapsulated in a single private method. In real production code, you may need to perform a series of internal refactorings before a pattern starts to emerge and you can extract an interface that aggregates several dependencies.

Now we've identified this behavior and cluster of dependencies, we can extract it into its own concern:
                                                                                         
```c#
public interface IOrderCollector
{
    void Collect(Order order);
}

public class OrderCollector : IOrderCollector
{
    private readonly IUserContext _userContext;
    private readonly IRateExchange _exchange;
    private readonly IAccountsReceivable _receivable;
 
    public OrderCollector(IAccountsReceivable receivable,
        IRateExchange exchange,
        IUserContext userContext)
    {
        // ...
    }
 
    public void Collect(Order order)
    {
        var user = _userContext.GetCurrentUser();
        var price = order.GetPrice(_exchange, _userContext);
        _receivable.Collect(user, price);
    }
}
```

The domain concern of order collection - which was before implicit - has now been made explicit. Now we can delegate this concern to its own service, our `OrderProcessor` class only requires 3 dependencies:

```c#
public class OrderProcessor
{
    public OrderProcessor(IOrderValidator validator,
       IOrderShipper shipper,
       IOrderCollector collector)
    {
       // ...
    }
       
    public SuccessResult Process(Order order)
    {
       bool isValid = _validator.Validate(order);
       if (isValid)
       {
           _collector.Collect(order);
           _shipper.Ship(order);
       }
    
       return CreateStatus(isValid);
    }
}
```

So the process goes like this:

> 1. Analyze how dependencies interact to identify clusters of behavior.
> 1. Extract an interface from these clusters [the Facade]
> 1. Copy the original implementation to a class that implements the new interface.
> 1. Inject the new interface into the consumer.
> 1. Replace the original implementation with a call the new dependency.
> 1. Remove the redundant dependencies.
> 1. Rinse and repeat :)

The beauty of this approach is that each component becomes almost trivial to test, as it does so little.

http://blog.ploeh.dk/2010/02/02/RefactoringtoAggregateServices/
