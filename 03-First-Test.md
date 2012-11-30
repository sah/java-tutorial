Running Your First Test
=====

Now that you've got a JUnit or TestNG Maven project created, let's run the tests that were created by the Maven 
archetype generation to make sure that everything works.

**Mac/Linux/Windows**

Run the following command from your `sauce-tutorial` directory:

    mvn test

This launches Maven and downloads the dependencies, compiles the source code, and runs the tests on the Sauce Labs 
cloud of Selenium servers. There are quite a few files to download so this may take a few minutes, then you'll see 
that JUnit or TestNG has started. You might not see any 
output instantaneously, but in a couple of minutes you'll see output similar to the following:
    
    -------------------------------------------------------
    T E S T S
    -------------------------------------------------------
    Running WebDriverDemoShootoutTest
    Tests run: 8, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 137.922 sec
    Running WebDriverTest
    Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 12.297 sec
    Running WebDriverWithHelperTest
    Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 13.156 sec
    
    Results :
    
    Tests run: 10, Failures: 0, Errors: 0, Skipped: 0
    
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------

Right now each test runs one at a time because the sample project created by the archetype isn't setup to run multiple 
tests in parallel, however we'll show how to enable parallel testing in one of our later tutorials. 

Try running the tests again by executing `mvn test`. While the tests are running, navigate to your 
[Sauce Labs tests page](https://saucelabs.com/tests) 
to see each test as it queues, runs, and finishes. Click on a running test on your tests page. You'll jump to a detail 
view where, if you caught the test while it was running you can watch Selenium controlling the browser. When the test 
finishes the page updates with a video of the test and a list of the Selenium commands that were sent.

If you don't catch a test while it's running, you can click the test's link on the 
[Sauce Labs tests page](https://saucelabs.com/tests) to see the video and the test's details.

Now that you know that your setup worked and you were able to run your first
test suite on the Sauce Labs cloud, let's look at what actually happened under the hood. 

If you open the `src/test/java/com/yourcompany/WebDriverTest.java` file in your text editor, this is what you'll see:

<!-- SAUCE:LOGIN -->

**JUnit**

```java
public class WebDriverTest {

    private WebDriver driver;

    @Before
    public void setUp() throws Exception {

        DesiredCapabilities capabilities = DesiredCapabilities.firefox();
        capabilities.setCapability("version", "17");
        capabilities.setCapability("platform", Platform.XP);
        this.driver = new RemoteWebDriver(
                new URL("http://<!-- SAUCE:USERNAME -->:<!-- SAUCE:ACCESS_KEY -->@ondemand.saucelabs.com:80/wd/hub"), capabilities);
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

Let's break down this `WebDriverTest` class block by block. 
* The `setUp()` method runs before every test in the class by virtue of the JUnit annotation `@Before`. 
* Next we create a Selenium `DesiredCapabilities` instance populated with the desired browser version 
and the desired platform.
* Then we create a Selenium `RemoteWebDriver` instance with two parameters: the DesiredCapabilities 
instance and a URL that points to `ondemand.saucelabs.com:80/wd/hub`.

```java
    @Before
    public void setUp() throws Exception {

    DesiredCapabilities capabilities = DesiredCapabilities.firefox();
    capabilities.setCapability("version", "17");
    capabilities.setCapability("platform", Platform.XP);
    this.driver = new RemoteWebDriver(
        new URL("http://<!-- SAUCE:USERNAME -->:<!-- SAUCE:ACCESS_KEY -->@ondemand.saucelabs.com:80/wd/hub"), capabilities);
    }
```

Here's our first test! As with all JUnit test methods, this test method uses the JUnit `Test` annotation `@Test`. 

This test accesses www.amazon.com and uses the JUnit `assertEquals` method to test whether or not the page title 
contains the string "Amazon.com: Online Shopping for Electronics, Apparel, Computers, Books, DVDs & more". 
In the world of [Java and WebDriver](http://selenium.googlecode.com/svn/trunk/docs/api/java/org/openqa/selenium/WebDriver.html#getTitle()), 
calling `driver.getTitle()` tells the Selenium session to return the title of the currently loaded page, and 
`assertEquals` compares the secified page title with the returned page title. Nothing happens if the titles are 
the same, but if the titles are different an exception is raised.


```java
	@Test
	public void webDriver() throws Exception {
	    driver.get("http://www.amazon.com/");
	    assertEquals("Amazon.com: Online Shopping for Electronics, Apparel, Computers, Books, DVDs & more", driver.getTitle());
	}
```

The `tearDown()` method runs after every test in the class is finished by virtue of the JUnit `After` 
annotation `@After`. Then we call `driver.quit()` to close the Selenium session.

```java
	@After
	public void tearDown() throws Exception {
	    driver.quit();
	}
```


**TestNG**

The `WebDriverTest` class created for TestNG looks very similar to the JUnit class. The main difference is that the 
TestNG class supplies the information for the desired browser settings as parameters using the TestNG
`Parameters` annotation `@Parameters` and the TestNG `Optional` annotation `@Optional`.

```java
public class WebDriverTest {

    private WebDriver driver;
	 
    @Parameters({"username", "key", "os", "browser", "browserVersion"})
    @BeforeMethod
    public void setUp(
        @Optional("<!-- SAUCE:USERNAME -->") String username,
        @Optional("<!-- SAUCE:ACCESS_KEY -->") String key,
        @Optional("XP") String os,
        @Optional("firefox") String browser,
        @Optional("17") String browserVersion, Method method) throws Exception {

            DesiredCapabilities capabilities = new DesiredCapabilities();
            capabilities.setBrowserName(browser);
            capabilities.setCapability("version", browserVersion);
            capabilities.setCapability("platform", Platform.valueOf(os));
            capabilities.setCapability("name", method.getName());
            this.driver = new RemoteWebDriver(
                new URL("http://" + username + ":" + key + "@ondemand.saucelabs.com:80/wd/hub"), capabilities);
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

WebDriverTest.java class demonstrates a basic Selenium test using JUnit and TestNG, now lets look at 
WebDriverWithHelperTest.java.

* _Next_: [Using the Java Helper library](##04-Java-Helper.md##)

