## Do no use stereotype-based registrations

- Stereotypes are a concept borrowed from the [Spring framework](https://therealdanvega.com/blog/2017/03/27/spring-stereotype-annotations).
- For instance, we may define a _Repository_ stereotype, denoting the role of a repository in the overall architecture at a conceptual, rather than implementation, level.
- We have experimented with registering components based on stereotype. For instance, we have had a `RegisterRepository` method that registers a component with the container as a Singleton, wrapped by a `MeasurementInterceptor`.
- Experience has shown us that such stereotype registrations are not well understood have been used inconsistently. For instance, many repos were not registered using `RegisterReposity`, and yet other _non_-repos _are_ registered using this method.
- We concluded that such an approach caused more problems than it solved. A simpler solution is to explicitly describe each component's characteristics upon registration. This way, we are free to register components as we choose, and the registrations API is kept simpler.

#### Don't

```c#
container.RegisterRepository<ICityInfoRepository, CityInfoRepository>()
```

#### Do

```c#
[RegisterSingleton(InterceptedBy = typeof(MeasurementInterceptor))]
public class CityInfoRepository : ICityInfoRepository
{ ... }
```
