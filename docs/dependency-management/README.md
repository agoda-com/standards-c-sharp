## Dependency management

### Register dependencies by convention using attributes

Rather than having a monstrous file where all our dependencies are registered explicitly, we will create Lifestyle attributes, with which your class can be decorated to have it automatically registered to the container.

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
