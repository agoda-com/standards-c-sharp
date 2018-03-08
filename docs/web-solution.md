# Web solution structure

This describes the standard abstract solution structure preferred by Agoda for a web-based project. Each box represents one project in your solution.

![Web solution structure](https://drive.google.com/uc?id=1zNxwpD89B4BP4Iiac8YQMCoJI2IkIt_3)

### View models / DTOs

- Should be dumb
- No dependencies
- If trivial, can include mapping logic as static method, eg `MyViewModel.From(MyModel model)`. Refactor this into a separate model builder as necessary.

