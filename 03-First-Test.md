Running Your First Test
=====

Now that you've got a JUnit or TestNG Maven project created, let's run the tests that were created by the Maven
archetype generation to make sure that everything works.

<!-- SAUCE:INCLUDE:run_maven -->

While the tests are running, navigate to your [Sauce Labs tests page](https://saucelabs.com/tests).
From there you'll be able to see each test as it queues, runs, and finishes.
You'll notice that each test has a name -- that information is sent
automatically at the beginning of each test.

Right now each test runs one at a time because the sample project created by the archetype isn't setup to run multiple tests in parallel, however we'll describe how to enable this in one of the later tutorials. For now,
take advantage of the serial nature of the tests and click on a running test
on your tests page. You'll jump to a detail view where, if you caught the
test while it was running, you'll be able to watch Selenium controlling the
browser. When the test finishes, the page updates with a video of the test
and a list of the various Selenium commands that were sent.

If you don't catch a test while it's running, you can click the test's link on the
[Sauce Labs tests page](https://saucelabs.com/tests) to see the test's details and video.

Now that you know that your setup worked and you were able to run your first
test suite on Sauce, let's look at what actually happened under the
hood. The simplest test is in the file
`src/test/java/com/yourcompany/WebDriverTest.java`.

The tests are very similar for both JUnit and TestNG, so we'll
only describe the JUnit test in detail.

<!-- SAUCE:LOGIN -->

**JUnit**

<!-- SAUCE:INCLUDE:junit_basic_test -->

Let's break this test class down, chunk by chunk. First, we use the
`setUp()` method to initialize the browser testing environment we will
need for the tests:

```java
    @Before
    public void setUp() throws Exception {
        // Choose the browser, version, and platform to test
        DesiredCapabilities capabilities = DesiredCapabilities.firefox();
        capabilities.setCapability("version", "5");
        capabilities.setCapability("platform", Platform.XP);
        // Create the connection to Sauce Labs to run the tests
        this.driver = new RemoteWebDriver(
                new URL("http://<!-- SAUCE:USERNAME -->:<!-- SAUCE:ACCESS_KEY -->@ondemand.saucelabs.com:80/wd/hub"),
                capabilities);
    }
```

This method is run before every test in the class (by virtue of the
JUnit `org.junit.Before` annotation). We create an
`org.openqa.selenium.remote.DesiredCapabilities` instance and use it
to specify the browser version and platform. We then create an
`org.openqa.selenium.remote.RemoteWebDriver` instance pointing at
`ondemand.saucelabs.com` and using the `DesiredCapabilities`
instance. This driver makes the tests use the specified browser and
platform running on Sauce Labs servers to execute the test.

Next, we write a simple test (annotated with org.junit.Test):

```java
    @Test
    public void webDriver() throws Exception {
        // Make the browser get the page and check its title
        driver.get("http://www.amazon.com/");
        assertEquals("Amazon.com: Online Shopping for Electronics, Apparel, Computers, Books, DVDs & more", driver.getTitle());
    }
```

The test accesses www.amazon.com and uses a [JUnit assertion](https://github.com/junit-team/junit/wiki/Assertions)
to check that the page title has the expected value. The call to
`driver.getTitle()` is a
[Selenium WebDriver method](http://selenium.googlecode.com/svn/trunk/docs/api/java/org/openqa/selenium/WebDriver.html#getTitle())
that tells Selenium to return the title of the
current page.

```java
	@After
	public void tearDown() throws Exception {
	    driver.quit();
	}
```

Finally, the `tearDown()` method is run after every test in the class (by virtue of the JUnit `org.junit.After` annotation).  We call `driver.quit()` to close the Selenium session.

**TestNG**

The TestNG version of the `WebDriverTest` class is very similar. THe
main difference is that the desired browser settings are provided as
parameters using the `org.testng.annotations.Parameters` and
`org.testng.annotations.Optional` annotations.

<!-- SAUCE:INCLUDE:testng_basic_test -->

Now that you know how to create a basic Selenium test, lets see how to
test against your own web app.

* _Next_: [Running tests against web applications](##04-Testing-Apps.md##)
