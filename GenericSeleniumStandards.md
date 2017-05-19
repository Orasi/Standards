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

# Expicit & Implicit Waits #

## Implicit Waits ##

Implicit waits set a timetout for the driver when polling the dom to look for an element. This not only effects the command findElement() or findElements() but will also effect commands such as isEnabled(), isVisible(), etc.  So if the timeout is set to 0, you will get a 'NoSuchElement' exception immediatly if the element is not found on the page.  If you set it to 10, then the driver will poll the dom for 10 secodns before throwing an exception.  

Suggested usage is to set the implicit timeout to a more generous amount, when the driver is first created.  

```
driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);
```

There may be times when you want to change the implicit wait during a test run.  For example, if you know all the elmenents are already rendered on the page, and you don't want the test hanging if an element isn't found, you can set the implicit wait back to 0 before working with an element.  A word of caution though - you will have to remember to set the imlicit wait back to the test default amount, otherwise it will be set to 0 for the remainder of the life of the driver (or if is changed somewhere else in the test).  

Good practice would be to have a test constant defined that holds the default implicit timeout for your project.  You will then refer to that constant anytime you want to change the imploicit wait back to the test default.  

```
driver.manage().timeouts().implicityWait(Constants.ELEMENT_TIMEOUT, TimeUnit.SECONDS)
```

## Explicit Waits ##

Explicit waits are for having the test automation halt execution while waiting for a condition to be met.  They can range from a hard wait (waiting for a specified time amount) or waiting for an element to be visible on the page

Hard wait:
```
Thread.sleep(1000)
```

In general, you want to avoid having hard waits in your test, as they are inefficient and can add up to a lot of wasted time over a test set execution.  There will be cases where using 1-3 second hard waits makes sense.

Instead of using hard waits, you will want to use the Expected Conditions class that selenium libraries provide.  

### Expected Conditions ###

Selenium provides an Expected Conditions class to help with object synching.  There are many available methods to use such as whether an element is visible, present, clickable, etc.  The java, c#, and python bindings come with a exepected conditions class.  See these links for all possible conditions:

Suggested usage is to combing the Expected conditions class with the WebDriverWait class.  The webdriverwait class extends a FluentWait class so that it will poll the dom every 500 ms to see whether the condition has been met.  

[WebDriverWait class](https://github.com/SeleniumHQ/selenium/blob/master/java/client/src/org/openqa/selenium/support/ui/WebDriverWait.java#L69)

Java usage example:
```
WebDriverWait wait = new WebDriverWait(driver,10);
        wait.until(ExpectedConditions.presenceOfElementLocated(By.xpath(elementXpath)));
```

#### Recommendations ####
You will want to house your own custom expected conditions methods separatly.  Using a wrapper method on the expected conditions will help clean up the page classes.  Additionally, the WebDriverWait with the ExpectedConditions class will throw a Timeout exception if the condition is not met.  The recommendation is to use a try/catch to catch the exception and report out in a more appropriate manner.  There are multiple ways to achieve this, below is a simple example in Java language:
```
public boolean waitForElementToBeVisible(WebElement element, int timeout){
	WebDriverWait wait = new WebDriverWait(driver, timeout);
	try {
		wait.until(ExpectedConditions.visibilityOf(element));
		return true;
	} catch (TimeoutException e){
		return false;
	}     
	}
```
----------

# Page Object Model (POM) #

The Page Object Model (POM) is an industry standard method of automation test code creation and organization.  

This methodology separates automation code into page classes and test classes.  Page classes represents the screens/pages/sections of a web app as a collection of objects and services the page provides.  Test classes represent the automated tests and will create instances of the page classes the test needs to interatct with.  

Page classes will generally contain all the objects on the page declared at the top followed by all the methods needed to interact with the page.  Generally, test validations (assertions) are left out of page classes, unless it makes sense to do so.  

Test Classes contain the flow of the test including all the validations(assertions)

Java usage example of a page class:
```
public class LoginPage {

	private By txtUseranme = By.id("username");
	private By txtPassord = By.id("password");
	private By btnLogin = By.name("loginbtn");
	private WebDriver driver;

	public LoginPage(WebDriver driver) {
		this.driver = driver;
	}

	public void userLogin(String userName, String password) {
		driver.findElement(txtUseranme).sendKeys(userName);
		driver.findElement(txtPassord).sendKeys(password);
		driver.findElement(btnLogin).click();
	}

}
```
Java usage example of a test class:
```
public class QuickTest {
  
	@Test
	public void f() {
		WebDriver driver = new FirefoxDriver();
		driver.get("your-application-url");
		
		LoginPage loginPage = new LoginPage(driver);
		Assert.assertTrue(loginPage.isLoginPageDisplayed(), "Verify login page is displayed");
		loginPage.userLogin("username", "password");
	}
}
```

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

If you need to search through a list of similar elements to find the one you need, try and see if using xpath or css selectors will achieve it.  

In this example - the driver has to repeatedly go to the webpage to grab the text from an element.

```
List<WebElement> filterList = driver.findElements(By.tagName("input"));
for (WebElement filterItem:filterList){
	if (filterItem.getText().equalsIgnoreCase("Blue"){
		element.click();
		break;
	}
}
```

The same can be achieved through one command.  
```
driver.findElement(By.xpath("//input[text='Blue']").click();
```
