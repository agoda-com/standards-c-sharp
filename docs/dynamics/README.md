## Dynamics

DO NOT USE DYNAMICS IN C#

- Dynamics were invented to allow the CLR to interoperate more easily with dynamically typed languages.
- We do not do this in Agoda - we move between Scala, C# and Typescript, all of which are statically typed. Therefore, we have no use case for dynamics.