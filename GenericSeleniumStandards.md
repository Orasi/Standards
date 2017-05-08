# Generic Selenium Standards #
----------
## Object Identification ##
Uniquely identifying objects is essential to having reliable and robust automated tests.  Selenium gives you different methods of identifying objects.  Below is the hierarchy of preferred methods, with the first being the most preferred and bottom being the least.  

 1. ID
 2. Name
 3. Relative Xpath
 4. CSS
 5. Class
 6. Tag
 7. Link Text
 
----------

## Page Object Model (POM)

The Page Object Model (POM) is an industry standard method of automation test code creation and organization.  

This methodology separates automation code into page classes and test classes.  Page classes represents the screens/pages/sections of a web app as a collection of objects and services the page provides.  Test classes represent the automated tests and will create instances of the page classes the test needs to interatct with.  

Page classes will generally contain all the objects on the page declared at the top followed by all the methods needed to interact with the page.  Generally, test validations (assertions) are left out of page classes, unless it makes sense to do so.  

Test Classes contain the flow of the test including all the validations(assertions)

### Recommendations ###

 - Use of page class for common areas in the application such as navigation bars, pop up pages such as forms, 
 - Use of wrapper methods in page classes to contain larger functionality of the page.  
	 - Example - createApplication() method.  Might contain the functionality of entering data in a form, clicking submit, and handling any alerts.  
