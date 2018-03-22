# Web solution structure

[Discuss here](../../../issues/2)

This describes the standard abstract solution structure preferred by Agoda for a web-based project. Each box represents one project in the solution. Use common sense and adapt to your specific use-case.

Prefer to organize code primarily by functional area, rather than architectural layer (see examples below).

![Web solution structure](https://drive.google.com/uc?id=1XPy--wohqCpiioi2oOz9IaerMoHlWA_h)

## Presentation project(s)

### Folder structure

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


### View models / DTOs

- Should be dumb and free of business logic, but may contain:
    - Trivial presentational logic (eg. formatting). Refactor into a presentational helper when necessary.
    - Trivial mapping logic, eg `MyViewModel.From(MyModel model)`. Refactor into a separate model builder when necessary.

### Views

- Should be dumb and free of business logic.
- May contain presentational logic.

### Controllers

- Should be thin wrappers around services, existing purely as a bridge between HTTP and the service layer.
- Should be free of business logic.
- Should accept and return View models / DTOs when communicating with the outside world.
- Should accept and return Models when communicating internally.

### Presentational helpers

- Static classes with pure methods that implement more complex presentational logic.

## Service project

Should be hosting environment agnostic - ie. should not depend on any HTTP abstractions.

### Folder structure

![Example folder structure](https://drive.google.com/uc?id=17Z1K6g5RA5eHzoT7QjiIxWfjLlzNnpW4)

### Models

- An abstract representation of part of our domain.

### Business services

- Implements business logic and builds models.
- May aggregate data from other services if required.
- Should take at least one non-pure dependency, else should be converted to a helper. If any methods can be made static, make them so, and consider moving them to a helper.
- Consider skipping the service entirely if it is only a thin wrapper around a repo or client library (eg. PAPI Client).

### Business helpers

- Static classes with pure methods that implement business logic.
- Prefer helpers over services for simplicity and to reduce constructor dependencies.

### Repo interfaces

- Should be defined here to make it easy to swap out repository implementations.

## Repository project

### Folder structure

![Example folder structure](https://drive.google.com/uc?id=1hDcozXoQIap_-Yxgoykoj8d2sajmyGUu)

### Repos

- Repo implementations.
- Can be combined with the Service project for convenience, or broken into a separate project to enforce correct chain of dependencies.
- May not be needed at all if you are only consuming backend services through client libraries (eg. PAPI Client).
