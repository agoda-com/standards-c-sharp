## Use of reflection

### Hard-coded string to identify types 
Do not use hard coded strings to dynamically instantiate classes. They make refactorings such as moving namespaces difficult.

#### Don't

```c#
// fails at runtime after change of namespace 
var instance Activator.CreateInstance("Agoda.Website.MVC", "Agoda.Website.MVC.MyType");
var type = Type.GetType("Agoda.Website.MVC.MyType")
```

#### Do

```c#
// caught at compile time after change of namespace 
var instance = Activator.CreateInstance(typeof(Agoda.Website.MVC.MyType));
var type = typeof(Agoda.Website.MVC.MyType);
```