# Web solution structure

[Discuss here](../../../../issues/2)

This describes the standard abstract solution structure preferred by Agoda for a web-based project. Each box represents one project in the solution. Use common sense and adapt to your specific use-case.

![Web solution structure](https://drive.google.com/uc?id=1ZobfNezweeamEs_FXFtp2e3xi8hvyb-Y)

## Presentation project(s)

### View models / DTOs

- Should be dumb and free of business logic, but may contain:
    - Trivial presentational logic (eg. formatting). Refactor into a presentational helper when necessary.
    - Trivial mapping logic, eg `MyViewModel.From(MyModel model)`. Refactor into a separate model builder when necessary.
- View models don't need to be defined if they merely duplicate a model's properties, i.e. avoid 1-to-1 matching classes.
- View models should include CMS contents required by a view, not models

### Views

- Should be dumb and free of business logic.
- May contain presentational logic.

### Controllers

- Should be thin wrappers around services, existing purely as a bridge between HTTP and the service layer.
- Should be free of business logic.
- Should accept and return View models / DTOs when communicating with the outside world.
- Should accept and return Models when communicating internally.
- Cannot be extended.

### Presentational helpers

- Static classes with pure methods that implement more complex presentational logic, eg.composing one or more models into view models

## Service project

Should be hosting environment agnostic - ie. should not depend on any HTTP abstractions.

### Services

- Implements business logic and builds models.
- May aggregate data from other other services if required.
- Should take at least one non-pure dependency and/or must do some form of I/O, else should be converted to a static helper. If any methods can be made static, make them so.
- Consider skipping the service entirely if it is only a thin wrapper around a repo or client library (eg. PAPI Client).

### Helpers

- Static classes with pure methods
- Prefer helpers over services for simplicity and to reduce constructor dependencies.

### Models

Models are meant to be reusable, their properties should not be tightly coupled with presentational needs and/or experimentation.

- An abstract representation of part of our domain.
- Should consist purely of data properties
- Should not implement any business logic internally
- Should never contain properties named after experiments; properties should always be meaningful regardless of the experiments that set their values
- Self-computable properties should never have a setter; their get method should implement the computation logic
- Must not contain any CMS values (with the sole exception of CMS that contains formatting and/or actual data); CMS should stay confined in view models
- Favor inheritance when possible, i.e. avoid duplicated models and/or duplicated properties across models

### Repos

- Classes that return raw data from backend systems.
- May not be needed at all if you are only consuming backend services through client libraries (eg. PAPI Client).

## Folder structure

- Keep top-level folders organized by high level feature, not by what a class is (e.g. repos vs models)
- Avoid organizing and naming after external dependencies
- Avoid organizing around pages
- Avoid unnecessary namespace span (e.g. Repositories vs Interfaces)

For example:

```
+- (Project root)
  +- Geography
    +- Models
      City
      Country
      Landmark
    +- Repositories
      GeoRepository
      IGeoRepository
  +- Pricing
    +- Models
      RoomPrice
    +- Repositories
      PriceRepository
      IPriceRepository
  +- Users
    +- Models
      BaseUser
      Traveller
      Host
    +- Repositories
      UserRepository
      IUserRepository
  +- Localization
    PriceFormatter
    DateFormatter
    +- Models
      Currency
      Language
    +- Repository
      CurrencyRepository
      ICurrencyRepository
      LanguageRepository
      ILanguageRepository
```
