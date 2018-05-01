## Philosophy: KISS

Obligatory trite quotes:

_Keep It Simple, Stupid_ - Some guy

_Everything should be made as simple as possible, but no simpler_ - Albert Einstein

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
