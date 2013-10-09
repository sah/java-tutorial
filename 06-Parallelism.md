Running Tests in Parallel
=====

As you may recall from earlier tutorials, Selenium tests can take a long time! They may take even longer on Sauce
because we start each test on a new virtual machine that has never been used before (don't worry, we don't charge
you for the spin-up time).

To make tests run faster, run more than one test at a time. As long as
the tests are independent --- whether you're running the same test
across different browsers or the tests just don't interact with each
other --- there should be no problem running them
simultaneously. Since we have thousands of
clean virtual machines on standby, we encourage you to run as many tests
as you can at once. For an overview of how many tests you can run in parallel, see the parallelization section of the
[Sauce plan page](http://saucelabs.com/pricing).

JUnit
===

Tests can be run in parallel using JUnit, but it takes a bit of work.
The [Java helper library](https://github.com/saucelabs/sauce-java) includes a `Parallelized`
class that creates a dynamic thread pool that holds each thread that is running a test.

**Parallelizing the WebDriverTest Class**

The following `WebDriverParallelTest` class demonstrates how to update
the `WebDriverTest` class so its tests run in parallel. The test is
parallelized by specifying the different parameters to test with, in this
case the browser and platform. Behind the scenes, the test framework
creates a different instance of the test class for each set of parameters
and runs them in parallel. The parameters are passed to the
constructor so each instance customizes it's behavior using those
parameters.

In this example, we're parallelizing tests across different browsers
on different platforms. Since testing an app in Firefox on Linux is
independent of testing it in Chrome on Windows, we can safely run both
tests in parallel. The static method `browsersStrings()` is
annotated with `org.junit.runners.Parameterized.Parameters`,
indicating it should be used to determine the parameters for each
instance of the test. The method returns a `LinkedList` of parameters
to use for each test instance's constructor. The
`WebDriverParallelTest` constructor captures these
parameters and `setUp()` uses them to configure the `DesiredCapabilities`.

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

As shown above (and as included in the sample project) only one
platform is returned, so only that one test will be run in
parallel. Let's fix that! Add a few more platforms or browser versions
(you might need to refer to [the Selenium
`org.openqa.selenium.Platform`
documentation](http://selenium.googlecode.com/git/docs/api/java/index.html?org/openqa/selenium/Platform.html)
to specify other platforms). Now, when you [run the
tests](##03-First-Test.md##), you should see these tests running in
parallel on the [Sauce Labs tests page](https://saucelabs.com/tests).


TestNG
===

TestNG has built in support for running tests in parallel that is configured by the following line in the
`src\test\resources\xml\testng.xml` file:

```xml
<suite name="ParallelTests" verbose="5" parallel="tests" thread-count="10">
```

For more information about the options available for running parallel tests using TestNG, see the
[TestNG website](http://testng.org/doc/documentation-main.html#parallel-running)

Next Steps
---

Parallelizing tests will make them run significantly faster. It is
only a little bit of work to parallelize them and it lets you test
your code for deployment much more quickly. Use parallelization to run
the same test across many browsers and platforms at once, or just to
run many tests that are independent simultaneously.

You're almost done! We have covered all the major functionality. Now,
we'll give you a few tips about how to get the best performance out of
Selenium and Sauce Labs.

* _Next_: [Tips for better Selenium test performance](##07-Tips.md##)
