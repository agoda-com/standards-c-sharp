## Philosophy: KISS

Obligatory trite quotes:

_Keep It Simple, Stupid_ - Some guy

_Everything should be made as simple as possible, but no simpler_ - Albert Einstein

    
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
