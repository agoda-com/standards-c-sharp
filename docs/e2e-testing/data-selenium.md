## Use the `data-selenium` attribute to identify your elements

Make it simpler to change HTML structure and CSS classes by identifying elements under test with the `data-selenium` HTML attribute, rather than HTML tags, classes or IDs. The addition of these attributes may slightly increase the size of the page, but when gzipped the difference will probably be negligable.  

#### Don't

Test is tighly coupled to structure of HTML and CSS class.

```html
<form>
    <button class="login-button"></button>
</form>
``` 

```c#
Driver.FindElement(By.CssSelector("form button.login-button"));
```

#### Do

Test is decoupled HTML and CSS, meaning we can easily change the page layout without breaking tests.

```html
<form>
    <button class="login-button" data-selenium="login-button"">Log in now</button>
</form>
``` 

```c#
// test is decoupled from 
Driver.FindElement(By.CssSelector("[data-selenium=login-button]"));
```