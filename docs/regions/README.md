## `#regions`

DO NOT USE REGIONS

- If you need regions to make your code understandable then your class / method is too big. Do not hide the problem inside regions, refactor it.
- Separating fields, properties, constructors, private methods etc into their own regions just adds noise, and should not be necessary if your class is well factored.
- In many editors, including Visual Studio, the region will appear collapsed by default, hiding the code within the region. It is generally bad practice to hide code by default, as this can lead to bad decisions as the code is maintained over time.