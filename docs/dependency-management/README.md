## Dependency management

Streamline registration of dependencies and minimize constructor paramters.

## Base class to hold common services

To reduce the number of constructor dependencies, create a base class (eg. `ServiceBase`) to hold commonly required services. Use the container resolution lifecycle to populate these properties after instantiation. Consider accessing these properties lazily in case they are not required by the child class. Here is a non-lazy version:

```c#
/// <summary>
/// A base class that just contains properties for common services - no other logic to be added.
/// </summary>
public abstract class ServiceBase
{
    protected IExperimentManager ExperimentManager { get; set; }
    protected CmsService CmsService { get; set; }
    // etc.
}
```

## Register dependencies by convention using attributes

Rather than having some monstrous file where all our dependencies are registered explicitly, we will create Lifestyle attributes, with which your class can be decorated to have it automatically registered to the container.

```c#
public abstract class LifestyleAttribute : Attribute
{ }

public class PerRequestLifestyle : LifestyleAttribute
{ }

public class SingletonLifestyle : LifestyleAttribute
{ }

public class TransientLifestyle : LifestyleAttribute
{ }
```
