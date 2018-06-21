## Do not check for presence elements before enumerating a collection

#### Don't

``` cs
// This check just adds noise. The foreach will not be entered if the collection is empty anyway.
if (!list.Any())
{
    return dataTable;
}

foreach (var hotel in list.Select((x, i) => new { Value = x, Index = i }))
{
    // update dataTable
}
```

#### Do

``` cs
foreach (var hotel in list.Select((x, i) => new { Value = x, Index = i }))
{
    // update dataTable
}
```