Tips for Better Selenium Test Performance
=====

This section discusses some ideas for improving the performance of Selenium tests.

Avoid Test Dependencies
---

Dependencies between tests prevent tests from being able to run in parallel. And running tests in parallel is by far
the best way to speed up the execution of your entire test suite. It's much easier to add a virtual machine than to 
try to figure out how to squeeze out another second of performance from a single test.

What are dependencies? Imagine if we had a test suite with these two tests:

```java
@Test
public void testLogin()
{
    // do some stuff to trigger a login
    assertEquals("My Logged In Page", driver.getTitle());
}

@Test
public void testUserOnlyFunctionality()
{
    driver.findElement(By.id("userOnlyButton")).click();
    assertEquals("Result of clicking userOnlyButton", driver.findElement(By.id("some_result")));
}
```

The `testLogin()` method in the first function's pseudocode triggers the browser to log in 
and asserts that the login was successful. The second test clicks a button on the
logged-in page and asserts that a certain result occurred.

This test class works fine as long as the tests run in order. But the
assumption the second test makes (that we are already logged in) creates a 
dependency on the first test. If these tests run at the same time, or if the
second one runs before the first test, the browser's cookies will
not allow Selenium to access the logged-in page and the second test fails.

The right way to remove these dependencies is to make sure that each test can 
run completely independently of the other. Let's fix the example above so
there are no dependencies:


```java
private void doLogin()
{
    // do some stuff to trigger a login
    assertEquals("My Logged In Page", driver.getTitle());
}

@Test
public void testLogin()
{
    doLogin();
}

@Test
public void testUserOnlyFunctionality()
{
    doLogin();
    driver.findElement(By.id("userOnlyButton")).click();
    assertEquals("Result of clicking userOnlyButton", driver.findElement(By.id("some_result")));
}
```

The main point is that it's dangerous to assume any state whatsoever when
developing tests for your app. Instead, find ways to quickly generate
desired states for individual tests the way we did in the `doLogin()` method above
-- it generates a logged-in state instead of assuming it. 

You might even want to develop an API for the development and test versions of your app
that provides URL shortcuts that generate common states. For example, 
a URL that's only available in test that creates a random user account and 
logs it in automatically.

Don't Use Brittle Locators
---
WebDriver provides a number of 
[locator strategies](http://code.google.com/p/selenium/wiki/JsonWireProtocol#/session/:sessionId/element) for accessing 
elements on a webpage. 

It's tempting to use complex XPath expressions like `//body/div/div/*[@class="someClass"]`
or CSS selectors like `#content .wrapper .main`. While these might work when 
you are developing your tests, they will almost certainly break when you make 
unrelated refactoring changes to your HTML output.

Instead, use sensible semantics for CSS IDs and form element names and try to 
restrict yourself to using `By.id()` or `By.name()`. This makes it 
much less likely that you'll inadvertently break your page by shuffling some 
lines of code around.

Use WebDriverWait
----
Selenium doesn't know when it's supposed to wait 
before executing the next command. When users click a submit button, 
they know not to try another action until the next page 
loads. Selenium, however, immediately executes the next command. If 
the next command is part of an assertion in your code the assertion may 
fail, even though if Selenium had waited a little bit longer the assertion would have succeeded.

The solution is to use 
[WebDriverWait](http://selenium.googlecode.com/svn/trunk/docs/api/java/org/openqa/selenium/support/ui/WebDriverWait.html), 
which in conjunction with an ExpectedConditions instance 
will wait until the expected condition is found before continuing to your next line in the test as shown below:

```java
WebDriverWait wait = new WebDriverWait(driver, 5); // wait for a maximum of 5 seconds
wait.until(ExpectedConditions.presenceOfElementLocated(By.id("pg")));
```

Using WebDriverWait is a great way to make tests less brittle and more
accepting of differences in network speeds, surges in traffic, and other challenges in the test environment.

* _Finally_: [Next steps and more information](##08-Info.md##)
