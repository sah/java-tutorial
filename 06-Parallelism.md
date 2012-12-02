Running Tests in Parallel
=====

As you may recall from earlier tutorials, Selenium tests can take a long time! They may take even longer on Sauce 
because we start each test on a new virtual machine that has never been used before (don't worry, we don't charge 
you for the spin-up time).

To make tests run faster, run more than one test at a time. Since we have thousands of
clean virtual machines on standby, we encourage you to run as many tests
as you can at once. For an overview of how many tests you can run in parallel, see the parallelization section of the 
[Sauce plan page](http://saucelabs.com/pricing).

JUnit
===

Tests can be run in parallel using JUnit, but it takes a bit of work. 
The [Java helper library](https://github.com/saucelabs/sauce-java) includes a `Parallelized` 
class that creates a dynamic thread pool that holds each thread that is running a test.

**Parallelizing the WebDriverTest Class**

The following `WebDriverParallelTest` class demonstrates how to update the `WebDriverTest` class so its tests run in parallel:

<!-- SAUCE:LOGIN -->
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
        browsers.add(new String[]{Platform.XP.toString(), "17", "firefox"});
		//add any additional browsers here
        return browsers;
    }

    private WebDriver driver;

    @Before
    public void setUp() throws Exception {

        DesiredCapabilities capabilities = new DesiredCapabilities();
        capabilities.setCapability(CapabilityType.BROWSER_NAME, browser);
        capabilities.setCapability(CapabilityType.VERSION, version);
        capabilities.setCapability(CapabilityType.PLATFORM, os);
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

TestNG
===

TestNG has built in support for running tests in parallel that is configured by the following line in the 
`src\test\resources\xml\testng.xml` file:

```xml
<suite name="ParallelTests" verbose="5" parallel="tests" thread-count="10">
```

For more information about the options available for running parallel tests using TestNG, see the 
[TestNG website](http://testng.org/doc/documentation-main.html#parallel-running)


* _Next_: [Tips for better Selenium test performance](##07-Tips.md##)

