## Service design

### Interfaces

- Only create an interface if:
  - multiple implementations exist in application code, or
  - the component needs to be mocked for testability.

Unnecessary interfaces pollute the codebase and make it more difficult to understand.
  
### Injection and mocking

- A component only needs to be mocked if it is _impure_, ie:
  - it has a side effect such as performing IO (eg. reading from disk, making a network call), or
  - it is non-deterministic - that is, it may return different results when called repeatedly with the same inputs (eg. `DateTime.Now`).
- An impure component will almost certainly need to mocked for unit testing. Therefore, it should be injected into its dependendants.

### Static methods

- A pure function operates in-memory and doesn't depend on external resources such as network calls, reading from hard disk etc. It therefore doesn't need to be mocked, swapped out, or injected. Create such functions as static.
- If something _can_ be static, then it probably _should_ be.
- If a component has both static and instance methods, then consider moving the static methods into their own helper class.
- More on [static methods](../static-methods/when-static.md) vs [instance methods](../static-methods/when-instance.md).   

### Dependencies

- Keep each component's dependency count to a reasonable level. The more dependencies, the more difficult the code is to change. An absolute maximum is 10, and a more reasonable level is 7 or fewer. See [Avoid constructor over injection](./avoid-constructor-over-injection.md) for more.
- Inject only _impure_ components as dependencies (see above). This helps reduce the dependency count, and Keeps It Simple.
- A high dependency count is a good indication that a component should be refactored into more narrowly focused responsibilities.
- Declare a component's dependencies explicitly in its constructor. Don't use property injection. It's [super lame](http://simpleinjector.readthedocs.io/en/latest/advanced.html#property-injection).
- Each injectable component should expose only a [single public constructor](https://www.cuttingedge.it/blogs/steven/pivot/entry.php?id=97). Multiple constructors lead to a fragile design and present maintainability issues. Use the [null object pattern](https://en.wikipedia.org/wiki/Null_object_pattern) for truly optional dependencies.
- Prefer [attribute based registration](attribute-based-registration.md) over explicit registration - eg. decorate your class with `[RegisterSingleton]` rather than registering directly with the container.
- **Do not use `DependencyResolver.Current`.** Inject your dependency through the constructor. [Read more](http://blog.ploeh.dk/2010/02/03/ServiceLocatorisanAnti-Pattern/).

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
    
    public MyController(MyViewModelMapper mapper, IMyService service)
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
    
    // Method is pure, so doesn't need to be mocked or injected -> make it static:
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
    
    // We removed an unnecessarily injected dependency!
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
