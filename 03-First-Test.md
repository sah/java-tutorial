Running your first test
=====

Now that you've got a Maven project created, let's try run the tests that were created by the archetype generation 
to make sure that everything works.

Run this command from your `sauce-tutorial` directory:

    mvn test

**Mac/Linux/Windows:**

This launches Maven and will download the dependencies, compiles the source code and run the tests. After a few 
moments you should see that JUnit/TestNG has started. You might not see any output instantaneously, but
eventually you will see the following output:

	------------------------------------------------------
	 T E S T S
	-------------------------------------------------------
	Running WebDriverTest
	Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 14.384 sec
	Running WebDriverWithHelperTest
	Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 14.743 sec

	Results :

	Tests run: 2, Failures: 0, Errors: 0, Skipped: 0

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
test suite on Sauce, let's look at what actually happened under the hood. If
you open the `src/test/java/com/yourcompany/WebDriverTest.java` file in your text editor, this is what
you'll see:

<!-- SAUCE:LOGIN -->

**JUnit**

```java
public class WebDriverTest {

    private WebDriver driver;

    @Before
    public void setUp() throws Exception {

        DesiredCapabilities capabillities = DesiredCapabilities.firefox();
        capabillities.setCapability("version", "5");
        capabillities.setCapability("platform", Platform.XP);
        this.driver = new RemoteWebDriver(
                new URL("http://<!-- SAUCE:USERNAME -->:<!-- SAUCE:ACCESS_KEY -->@ondemand.saucelabs.com:80/wd/hub"),
                capabillities);
    }

    @Test
    public void webDriver() throws Exception {
        driver.get("http://www.amazon.com/");
        assertEquals("Amazon.com: Online Shopping for Electronics, Apparel, Computers, Books, DVDs & more", driver.getTitle());
    }

    @After
    public void tearDown() throws Exception {
        driver.quit();
    }

}
```

Let's break this test class down, chunk by chunk:

```java
	@Before
	public void setUp() throws Exception {

    	DesiredCapabilities capabillities = DesiredCapabilities.firefox();
	    capabillities.setCapability("version", "5");
	    capabillities.setCapability("platform", Platform.XP);
	    this.driver = new RemoteWebDriver(
	            new URL("http://<!-- SAUCE:USERNAME -->:<!-- SAUCE:ACCESS_KEY -->@ondemand.saucelabs.com:80/wd/hub"),
	            capabillities);
	}
```

The `setUp()` method is run before every test in the class (by virtue of the JUnit `org.junit.Before` annotation). Here, we
create a `org.openqa.selenium.remote.DesiredCapabilities` instance populated with the browser version and platform we
wish to use.  We then create a `org.openqa.selenium.remote.RemoteWebDriver` instance using the DesiredCapabilities instance,
and with a URL that points to ondemand.saucelabs.com.

```java
	@Test
	public void webDriver() throws Exception {
	    driver.get("http://www.amazon.com/");
	    assertEquals("Amazon.com: Online Shopping for Electronics, Apparel, Computers, Books, DVDs & more", driver.getTitle());
	}
```

Our first test! (Notice that all test functions are annotated with org.junit.Test). This test access www.amazon.com, and
uses a [JUnit assertion]
to check whether the page title contains the string "Amazon.com: Online Shopping for Electronics, Apparel, Computers, Books, DVDs & more. In the
world of [Java and WebDriver](http://selenium.googlecode.com/svn/trunk/docs/api/java/org/openqa/selenium/WebDriver.html#getTitle()),
calling `driver.getTitle()` tells the Selenium session to return the title of the
currently-loaded page.

```java
	@After
	public void tearDown() throws Exception {
	    driver.quit();
	}
```

The `tearDown()` method is run after every test in the class (by virtue of the JUnit `org.junit.After` annotation).  We call `driver.quit()` to close the Selenium session.

**TestNG**

The WebDriverTest class that gets created when the quickstart-webdriver-testng archetype is used looks pretty similar to the JUnit class.

```java
public class WebDriverTest {

   	private WebDriver driver;
	 
    @Parameters({"username", "key", "os", "browser", "browserVersion"})
    @BeforeMethod
    public void setUp(@Optional("<!-- SAUCE:USERNAME -->") String username,
                      @Optional("<!-- SAUCE:ACCESS_KEY -->") String key,
                      @Optional("mac") String os,
                      @Optional("iphone") String browser,
                      @Optional("5.0") String browserVersion,
                      Method method) throws Exception {

        DesiredCapabilities capabillities = new DesiredCapabilities();
        capabillities.setBrowserName(browser);
        capabillities.setCapability("version", browserVersion);
        capabillities.setCapability("platform", Platform.valueOf(os));
        capabillities.setCapability("name", method.getName());
        this.driver = new RemoteWebDriver(
                new URL("http://" + username + ":" + key + "@ondemand.saucelabs.com:80/wd/hub"),
                capabillities);
    }

    @Test
    public void webDriver() throws Exception {
        driver.get("http://www.amazon.com/");
        assertEquals("Amazon.com: Online Shopping for Electronics, Apparel, Computers, Books, DVDs & more", driver.getTitle());
    }

    @AfterMethod
    public void tearDown() throws Exception {
        driver.quit();
    }

}
```

The main difference is that TestNG class has supplied the information for the desired browser settings as parameters using the `org.testng.annotations.Parameters` and `org.testng.annotations.Optional` annotations.

The WebDriverTest.java file demonstrates a basic Selenium test using JUnit/TestNG, now lets look at the WebDriverWithHelperTest.java class.

* _Next_: [Running tests against web applications](##04-Testing-Apps.md##)

