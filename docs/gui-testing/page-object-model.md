## Use a Page Object to interact with your app

Isolate your GUI-driven tests from the GUI itself with the Page Object pattern.

- Abstract the structure of your page away from the actual tests by storing all selectors or interactions unique toyour page or component in a Page Object.
- If the structure of your HTML or CSS changes, ideally you will now need only minor changes to your Page Object to keep tests passing. Without the Page Object abstraction, you may have a great number of CSS selectors baked into your tests that must be changed.
  
> The basic rule of thumb for a page object is that it should allow a software client to do anything and see anything that a human can. It should also provide an interface that's easy to program to and hides the underlying widgetry in the window. So to access a text field you should have accessor methods that take and return a string, check boxes should use booleans, and buttons should be represented by action oriented method names. The page object should encapsulate the mechanics required to find and manipulate the data in the gui control itself. - [Martin Fowler](https://martinfowler.com/bliki/PageObject.html)

#### Don't

Page URLs and selectors are hard coded into the test. We are also tighly bound to the fairly verbose Selenium Web Driver API.

```c#
[Test]
public void LoginButton_WhenClicked_FocusesUsernameTextbox()
{
    Driver.Navigate().GoToUrl("https://qa-site/home.html");
    var showLoginFormButton = Driver.FindElement(By.CssSelector("[data-selenium=show-login-button]"));
    var textBox = Driver.FindElement(By.CssSelector("[data-selenium=username-textbox]"));
    
    showLoginFormButton.Click();
    
    Assert.IsTrue(Driver.SwitchTo().ActiveElement().Equals(textBox));
}
```

#### Do

Page URLs and selectors are separated from test. Our Page Object provides a clean, abstract API to interact with our page.

```c#

public abstract class PageObjectBase
{
    protected void Navigate(string url)
    {
        Driver.Navigate().GoToUrl(url);
    }
    
    public bool IsFocused(IWebElement element)
    {
        return Driver.SwitchTo().ActiveElement().Equals(element);
    }
}

public class HomePageObject : PageObjectBase
{
    private const string URL = "https://qa-site/home.html";
    private const SHOW_LOGIN_BUTTON_SELECTOR = "[data-selenium=show-login-button]";
    private const USERNAME_TEXTBOX_SELECTOR = "[data-selenium=username-textbox]";
    
    public void Navigate()
    {
        Navigate(URL);
    }
    
    public IWebElement ShowLoginFormButton
    {
        get 
        { 
            return Driver.FindElement(By.CssSelector(SHOW_LOGIN_BUTTON_SELECTOR));
        };
    }
    
    public IWebElement UsernameTextBox
    {
        get 
        { 
            return Driver.FindElement(By.CssSelector(USERNAME_TEXTBOX_SELECTOR));
        };
    }
}

[Test]
public void LoginButton_WhenClicked_FocusesUsernameTextbox()
{
    var homePage = new HomePageObject();
    homePage.Nagivate();
    
    homePage.ShowLoginFormButton.Click();
    
    Assert.IsTrue(homePage.IsFocused(homePage.UsernameTextBox));
}

```