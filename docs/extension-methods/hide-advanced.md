## Use extension methods to hide advanced functionality

- If you are adding advanced or esoteric functionality to your class that will be used by only a small subset of your users, then consider adding it as an extension method.
- This keeps things simple for new users who won't need these advanced scenarios (yet), but makes it available to those who do.
- Create such methods in a namespace such as "Advanced" to allow advanced users to opt in. 