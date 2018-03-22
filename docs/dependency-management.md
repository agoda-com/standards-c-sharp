# Dependency management

[Discuss](../../../code-standards/issues/3)

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

To register your class with the container, decorate it with one of the Lifestyle attributes:

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

## Make things static where they can be

If your function is pure (ie. same output for the same input, and no side effects), create it as a static method. Do not inject it as a dependency.

## Nomenclature

Reserve the suffix `Service` for a class with non-pure methods that requires dependencies. If a class consists only of pure methods, then make it static and name it something else, eg. `FooHelper`, `FooUtils` etc.
