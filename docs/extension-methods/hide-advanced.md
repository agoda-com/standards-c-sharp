## Use extension methods to hide advanced functionality

- If you are adding advanced or esoteric functionality to your class that will be used by only a small subset of your users, then consider using extension methods.
- This keeps the API simple for new users who probably won't need these advanced scenarios (yet), but makes it available to those who do.
- Create these methods in a namespace like "Advanced" to allow such users to opt in. 