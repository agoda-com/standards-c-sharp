## If it's unlikely to change, don't make it configurable

If a value isn't likely to change over time, or between environments, then don't put it into configuration. Use a constant instead.

#### Don't

```xml
<add key="ssrPageUrl" value="/{0}pages/agoda/default/DestinationSearchResult.aspx?asq={1}" />
```

#### Do

```c#
public const string SSR_PAGE_URL = "/{0}pages/agoda/default/DestinationSearchResult.aspx?asq={1}"; 
```