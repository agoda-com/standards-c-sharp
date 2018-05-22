## Dependency management

### Register dependencies declaratively using attributes

- Avoid a huge unmaintanable config file where all our dependencies are registered explicitly.
- Prefer to perform simple container registrations using an attribute.

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
