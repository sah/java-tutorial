Running tests in parallel
=====

As you may recall from earlier tutorials, Selenium tests can take a long
time! They may take even longer on Sauce because we start each test
on a new virtual machine that has never been used before (don't worry, we don't charge you for the spin-up time).

The solution to this is to run more than one test at a time. Since we have thousands of
clean virtual machines on standby, we encourage you to run as many tests
as you can at once. For an overview of how many tests you can run in parallel, see the parallelization section of our
[plan page](http://saucelabs.com/pricing).

**TestNG**

TestNG has built in support for running tests in parallel.  This can be configured in the `src\test\resources\xml\testng.xml` file for your tests:

```xml
<suite name="ParallelTests" verbose="5" parallel="tests" thread-count="10">
```

Further information on the options available for running parallel tests using TestNG can be found at the [TestNG website](http://testng.org/doc/documentation-main.html#parallel-running)

**JUnit**

Tests can be run in parallel using JUnit, but it takes a little bit more work.  The [Java helper libraries](https://github.com/saucelabs/sauce-java) include a `com.saucelabs.junit.Parallelized` class, which creates a dynamic thread pool which will be used to hold each thread that is running a test.

The following class demonstrates how to update the WebDriverTest to facilitate being run in parallel.

```java
@RunWith(Parallelized.class)
public class WebDriverParallelTest {

    private String browser;
    private String os;
    private String version;

    public WebDriverParallelTest(String os, String version, String browser) {
        super();
        this.os = os;
        this.version = version;
        this.browser = browser;
    }

    @Parameterized.Parameters
    public static LinkedList browsersStrings() throws Exception {
        LinkedList browsers = new LinkedList();
        browsers.add(new String[]{Platform.MAC.toString(), "5.0", "iPhone"});
		//add any additional browsers here
        return browsers;
    }

    private WebDriver driver;

    @Before
    public void setUp() throws Exception {

        DesiredCapabilities capabillities = new DesiredCapabilities();
        capabillities.setCapability(CapabilityType.BROWSER_NAME, browser);
        capabillities.setCapability(CapabilityType.VERSION, version);
        capabillities.setCapability(CapabilityType.PLATFORM, os);
        this.driver = new RemoteWebDriver(
                new URL("http://<your-user-name>:<your-access-key>@ondemand.saucelabs.com:80/wd/hub"),
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

* _Next_: [Tips for better Selenium test performance](https://github.com/saucelabs/java-tutorial/blob/master/07-Tips.md)

