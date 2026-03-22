---
name: csharp-gui-testing
description: >-
  C# GUI/Selenium testing standards. Covers CSS selectors over XPath,
  data-selenium attributes for element identification, Page Object Model
  pattern. Use when writing or reviewing Selenium-based GUI tests, browser
  automation tests, or UI test infrastructure in C#.
---

# C# GUI Testing Standards

## Use CSS selectors, not XPath

XPath is unfamiliar to most developers and adds noise. Since we identify elements with `data-selenium`, CSS selectors are sufficient.

```c#
// Don't
Driver.FindElements(By.XPath(".//*[@data-selenium='hotel-item']"));

// Do
Driver.FindElements(By.CssSelector("[data-selenium=hotel-item]"));
```

## Use `data-selenium` attribute for element identification

Decouple tests from HTML structure and CSS classes. Identify elements with a dedicated `data-selenium` attribute.

```html
<!-- Don't — coupled to CSS class and HTML structure -->
<form>
    <button class="login-button">Log in</button>
</form>
```

```c#
Driver.FindElement(By.CssSelector("form button.login-button"));
```

```html
<!-- Do — decoupled via data attribute -->
<form>
    <button class="login-button" data-selenium="login-button">Log in</button>
</form>
```

```c#
Driver.FindElement(By.CssSelector("[data-selenium=login-button]"));
```

## Use Page Object Model

Isolate test code from the GUI with the Page Object pattern. All selectors and page-specific interactions live in a Page Object class.

```c#
// Don't — selectors and URLs hard-coded in tests
[Test]
public void LoginButton_WhenClicked_FocusesUsernameTextbox()
{
    Driver.Navigate().GoToUrl("https://qa-site/home.html");
    var button = Driver.FindElement(By.CssSelector("[data-selenium=show-login-button]"));
    button.Click();
    // ...
}

// Do — abstracted into a Page Object
public class HomePageObject : PageObjectBase
{
    private const string URL = "https://qa-site/home.html";

    public void Navigate() => Navigate(URL);

    public IWebElement ShowLoginFormButton =>
        Driver.FindElement(By.CssSelector("[data-selenium=show-login-button]"));

    public IWebElement UsernameTextBox =>
        Driver.FindElement(By.CssSelector("[data-selenium=username-textbox]"));
}

[Test]
public void LoginButton_WhenClicked_FocusesUsernameTextbox()
{
    var homePage = new HomePageObject();
    homePage.Navigate();
    homePage.ShowLoginFormButton.Click();
    Assert.IsTrue(homePage.IsFocused(homePage.UsernameTextBox));
}
```

Benefits:

- HTML/CSS changes only require Page Object updates, not test changes
- Clean, abstract API for interacting with pages
- Page Object should let a software client do anything a human can
