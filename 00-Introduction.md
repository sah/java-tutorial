Sauce Labs, Selenium WebDriver, and Java Tutorials
============

Ready to get your project up and running with Selenium WebDriver and Sauce
Labs? You've come to the right place. If you are new to Selenium, you might
want to jump down and have a look at the [complete tutorial](#complete).

Maven
----
We recommend that you use [Maven](http://maven.apache.org) to handle the build
and deployment of your Java projects. 

We'll assume for this tutorial that you have a recent (&gt;=Java 6) version of Java
installed on your system. If you need to get that set up, check out the
[complete setup tutorial](##02-Setup.md##). Now on to getting Maven:

<!-- SAUCE:INCLUDE:maven_setup -->

Run your first test suite
---
Now that Maven is installed and your archetype is created, let's run our first suite of tests in parallel,
with the following command (`paratest` is simply a wrapper around PHPUnit that
allows us to run multiple sessions in parallel):

<!-- SAUCE:INCLUDE:run_maven -->

While the tests are running head to your [Sauce Labs account
page](https://saucelabs.com/account) and you can see them running.
You can even watch them running live if you want by clicking the test names!

Understand the test framework
---

Let's have a look at what we just tested. If you want, open up
`src/test/java/com/yourcompany/WebDriverTest.java` in your own editor to follow along. Here's the code without
the test logic itself, so you can see how we're setting up the test suite:

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

**TestNG**

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


A few things to note:

1.  This is a basic test which uses the JUnit/TestNG test annotations.  The `src/test/java/com/yourcompany/WebDriverWithHelperTest.java`
	which is covered in the [Java Helper](##04-Java-Helper.md##) section of the tutorial
    is where all of our Sauce-specific functionality comes from (like test
    pass/fail reporting, automatic test naming, etc...)
2.  We tell Sauce the browser and platform we want to use by setting . (To see all
    the different possibilities, check out Sauce's
    [browsers](https://saucelabs.com/docs/browsers) page).

And that's all we need to do to get the test suite running on Sauce! If you're
interested in the details of the test functions themselves, check out the
[First Test](##03-First-Test.md##) section of the complete tutorial.

Wrapping up and next steps
---
For more information, please explore the various documentation available:

<!-- SAUCE:INCLUDE:docs -->

If this tutorial went by a little too quickly, look below for the complete tutorial, where we go into
much more detail about how Selenium and Sauce Labs work, and how to avoid
certain pitfalls along the road to becoming a functional testing ninja.

<a name="complete"></a>Complete Tutorial
---
Want a bit more background on Selenium and Sauce Labs, or are you starting
a new project and want to do it right from the beginning? These tutorials
explain how to use Java tests to run automated Selenium WebDriver tests on the
Sauce Labs cloud of Selenium servers.

We assume that you have some familiarity with Java, the command line
interface in your operating system, and the fundamentals of automated testing.
However, even if this is your first time working with Java and automated testing
you should be able to successfully follow these step-by-step instructions.

Let's get started! Click the first link below.

* [How Selenium WebDriver and Sauce Labs work](##01-Selenium.md##)
* [Setting up your system to use Sauce with Java](##02-Setup.md##)
* [Running your first test](##03-First-Test.md##)
* [Running tests against web applications](##04-Testing-Apps.md##)
* [Using the Sauce Java Helper libraries](##04-Java-Helper.md##)
* [Testing local apps with Sauce Connect](##05-Sauce-Connect.md##)
* [Running tests in parallel](##06-Parallelism.md##)
* [Tips for better Selenium test performance](##07-Tips.md##)
* [Next steps and more information](##08-Info.md##)
