# Object Identification #
Uniquely identifying objects is essential to having reliable and robust automated tests.  Selenium gives you different methods of identifying objects.  Below is the hierarchy of preferred methods, with the first being the most preferred and bottom being the least.  

 1. ID
 2. Name
 3. Relative Xpath
 4. CSS
 5. Class
 6. Tag
 7. Link Text
 
## XPath Recommendations ##
 
In general, you will want to avoid using the absolute xpath of an element.
 
**Absolute**: It is a complete path of an element starting at the root element all the way to the desired element.  It starts with a single forward slash (/).  While this is the faster way to locate an element by xpath it is not reliable.  If the structure of the html changes, then the xpath you have used will be broken.  Example:

```/html/body/form[1]```

**Relative**: The path starts with the desired element and goes from there.  It is slower than using absolute, but is more reliable and you are less likely to have to update your code.  Example:
```//form[@id='loginForm']```

If you are working with an element that does not have a unique identifier, the recommendations are to find a nearby element with an unique ID, and to use the relationship in your xpath expression.  This is achieved using xpath axis such as parent, child, preceding-sibling, etc.  See the xpath tutorial for more details.  

----------

# Page Object Model (POM) #

The Page Object Model (POM) is an industry standard method of automation test code creation and organization.  

This methodology separates automation code into page classes and test classes.  Page classes represents the screens/pages/sections of a web app as a collection of objects and services the page provides.  Test classes represent the automated tests and will create instances of the page classes the test needs to interatct with.  

Page classes will generally contain all the objects on the page declared at the top followed by all the methods needed to interact with the page.  Generally, test validations (assertions) are left out of page classes, unless it makes sense to do so.  

Test Classes contain the flow of the test including all the validations(assertions)

## Recommendations ##

 - Use of page class for common areas in the application such as navigation bars, pop up pages such as forms, 
 - Use of wrapper methods in page classes to contain larger functionality of the page.  
	 - Example - createApplication() method.  Might contain the functionality of entering data in a form, clicking submit, and handling any alerts.  
	 
# Page Factory Model #
	 
----------

# General Recommendations #

Try and limit the amount of times the webdriver has to fetch something from a page.  This will improve the performance of your test.  There are several cases for this:

If you need to use the text of an element more than once, use a variable to hold the text value instead of repeatedly using the driver.  Example:

```String text = driver.FindElementBy(id = “someID”).getText();```

If you need to search through a list of elements to find the one you need, try and see if using xpath or css selectors will achieve it.  

In this example - the driver has to repeatedly go to the webpage, find the element, and grab the text. 
