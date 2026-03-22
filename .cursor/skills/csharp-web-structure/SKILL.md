---
name: csharp-web-structure
description: >-
  C# web solution project structure standards. Covers presentation layer, view
  models, controllers, services, helpers, models, repositories, folder
  organization by feature. Use when setting up a new C#/.NET web project
  structure, organizing solution projects, or deciding where code belongs in
  the solution hierarchy.
---

# C# Web Solution Structure Standards

## Presentation project

### View models / DTOs

- Dumb, free of business logic
- May contain trivial presentational/mapping logic
- Only view models may be serialized to the client (never models/entities)
- Should include CMS content required by the view

### Views

- Dumb, free of business logic
- May contain presentational logic

### Controllers

- Thin wrappers around services — bridge between HTTP and service layer
- Free of business logic
- Accept/return view models for browser communication
- Accept/return models for service layer communication

### Presentational helpers

- Static classes with pure methods for complex presentational logic (e.g., composing models into view models)

## Service project

Must be hosting-environment agnostic — no HTTP abstractions.

### Services

- Implement business logic and build models
- May aggregate data from other services
- Must take at least one non-pure dependency and/or do I/O, else convert to static helper
- Consider skipping if only a thin wrapper around a repo or client library

### Helpers

- Static classes with pure methods
- Prefer helpers over services for simplicity and fewer constructor dependencies

### Models

- Abstract representation of the domain
- Data properties only — no business logic
- No properties named after experiments
- Self-computable properties: no setter, compute in getter
- No CMS values (exception: CMS containing formatting/data)
- Favor inheritance to avoid duplication

### Repos

- Return raw data from backend systems (entities)
- May not be needed if consuming through client libraries

## Folder structure

- Top-level folders organized by **feature**, not by class type
- Avoid organizing around external dependencies or pages

```
+- Geography
  +- Models (City, Country, Landmark)
  +- Repositories (GeoRepository, IGeoRepository)
+- Pricing
  +- Models (RoomPrice)
  +- Repositories (PriceRepository, IPriceRepository)
+- Users
  +- Models (BaseUser, Traveller, Host)
  +- Repositories (UserRepository, IUserRepository)
+- Localization
  PriceFormatter, DateFormatter
  +- Models (Currency, Language)
  +- Repository (CurrencyRepository, LanguageRepository)
```
