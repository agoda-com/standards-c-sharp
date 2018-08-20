## Do not use hard coded string to identify types

Do not use hard coded strings to identify namespaces and types. They make refactorings such as moving namespaces or renaming types harder, and failures will be caught at runtime instead of compile time.

#### Don't

```c#
// both fail at runtime after change of namespace 
var instance Activator.CreateInstance("Agoda", "Agoda.MyType");
var type = Type.GetType("Agoda.MyType")
```

#### Do

```c#
// caught at compile time after change of namespace 
var instance = Activator.CreateInstance(typeof(Agoda.MyType));
var type = typeof(Agoda.MyType);
```
