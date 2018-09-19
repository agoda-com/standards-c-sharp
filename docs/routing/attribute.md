## Use attribute-based routing instead of convention-based routing

- Convention-based routing tightly couples your code to your URLs. By default, renaming an action method would change
the URL it handles.
- Attribute-based routing decouples your class and method names from URLs, giving you the freedom to refactor
without breaking the site, or at least having to implement redirects.
- Attribute-based routing is arguably easier to understand, as the URL appears right above the code to which it points.
- For these reasons, always use attribute-based routing when exposing HTTP endpoints.

#### Don't

```c#
public class Global
{
    public void Application_Start()  
    {   
       RouteConfig.RegisterRoutes(RouteTable.Routes);  
    }
    
    public static void RegisterRoutes(RouteCollection routes)  
    {  
        routes.IgnoreRoute("{resource}.axd/{*pathInfo}");  

        routes.MapRoute(  
            name: "Default",  
            url: "{controller}/{action}/{id}",  
            defaults: new { controller = "Home", action = "Index", id = UrlParameter.Optional }  
        );  
    }  
}  
```

#### Do
```c#
public class HomeController: Controller  
{  
    [Route("{department}/employees/{employeeId ?}")]  
    public string Employee(string department, int? employeeId)  
    {  
        ...
    }  
}  
```