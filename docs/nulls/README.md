## nulls and IEnumerable

### Do not return null to represent an empty enumerable

#### Don't

```c#
public IEnumerable<int> GetPropertyIds(int hostId)
{
    var properties = propertyService.GetPropertiesForHost(hostId);
    
    if (properties == null || !properties.Any()) 
    {
        return null; 
        // Now the caller has to somehow know to deal with this special case.
        // You are asking for a NullReferenceException in prod.
    }
    
    return properties.Select(p => p.Id);
}
```

#### Do

```c#
public IEnumerable<int> GetPropertyIds(int hostId)
{
    var properties = propertyService.GetPropertiesForHost(hostId);
    
    if (properties == null) 
    {
        // Just return an empty enumerable and everything should just work.
        return Enumerable.Empty<int>();
    }
    
    return properties.Select(p => p.Id);
}
```

#### Even better

```c#
public IEnumerable<int> GetPropertyIds(int hostId)
{
    // Fix propertyService.GetProperties() to never return null itself, and we
    // can skip the null check entirely.
    return propertyService
        .GetPropertiesForHost(hostId)
        .Select(p => p.Id);
}
```