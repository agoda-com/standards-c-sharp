## Do not use `dynamic`

- Dynamics skip compile time type-checking at the expense of potential runtime errors.
- Dynamics were introduced to allow the CLR to interoperate more easily with dynamically typed languages.
- We do not do this in Agoda - we move between Scala, C# and Typescript, all of which are statically typed. Therefore, we have no use case for dynamics.
