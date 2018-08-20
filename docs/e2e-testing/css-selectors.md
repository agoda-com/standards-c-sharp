## Use CSS-selectors instead of XPath to access elements

- We are all familiar with CSS selectors, but many of us are not comfortable with XPath.
- As we should [only be identifying elements under test with the `data-selenium` attribute](data-selenium.md), using XPath just adds noise and confusion, so should not be used.

 #### Don't
 
 ```c#
 // What do all those characters even mean? I don't even want to have to know.
 Driver.FindElements(By.XPath(".//*[@data-selenium='hotel-item']")) 
 ```
 
 #### Do
 
 ```c#
 Driver.FindElements(By.CssSelector("[data-selenium=hotel-item]"))
 ```