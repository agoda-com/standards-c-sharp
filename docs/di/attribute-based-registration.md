## Register components with DI using Attributes

- Avoid a huge unmaintanable config file where all our dependencies are registered explicitly.
- Prefer to perform simple container registrations using an attribute.

### Simple registrations
```c#
[RegisterSingleton]  // equivalent to ContainerControlledLifetimeManager – one instance shared for the lifetime of the application
[RegisterPerRequest] // equivalent to HttpContextLifetimeManager – one instance created and shared for each web request
[RegisterTransient]  // equivalent to TransientLifetimeManager – one instance created each time requested from the container, never shared
```

#### Don't

```
container.RegisterType<MyClass, MyInterface>(new ContainerControlledLifetimeManager());
````

#### Do
```c#
[RegisterSingleton]
public class MyClass : MyInterface 
{
  ...
}
```

### Mocking

#### Don't
``` c#
if (!Configuration.IsMock)
{
  container.RegisterType<IMyService, MyService>();
}
else
{
  container.RegisterType<IMyService, MyServiceMock>();
}
```

#### Do
``` c#
[RegisterTransient(MockImplementation = typeof(MyServiceMock))] 
public class MyService : IMyService
{
}
```