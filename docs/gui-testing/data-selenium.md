## Use the `data-selenium` attribute to identify your elements

Make it simple to change HTML structure and CSS classes by identifying elements under test with the `data-selenium` HTML attribute, rather than HTML tags, classes or IDs. The addition of `data-selenium` attributes may slightly increase the size of the page, but when gzipped the difference will probably be negligable. We believe the trade off in developer productivity is worth it.

#### Don't

Test is tighly coupled to structure of HTML and CSS class.

```html
<form>
    <button class="login-button">Log in</button>
</form>
``` 

```c#
Driver.FindElement(By.CssSelector("form button.login-button"));
```

#### Do

Test is decoupled from HTML and CSS, meaning we can easily change the page layout without breaking any tests.

```html
<form>
    <button class="login-button" data-selenium="login-button"">Log in</button>
</form>
``` 

```c#
Driver.FindElement(By.CssSelector("[data-selenium=login-button]"));
```