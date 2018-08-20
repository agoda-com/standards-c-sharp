## Monads

Do not use monads in C#. While they might elegantly solve a certain set of problems, we do not (yet?) have the knowledge or ability to use them consistently, clearly and correctly across all teams in production code.

Note that certain monadic types are built in to C#, such as `Nullable<T>`. This is fine to use itself, but do not go further by defining your own `Option<T>`, plus a bind function, etc.