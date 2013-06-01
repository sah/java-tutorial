Sauce Labs, Selenium WebDriver, and Java Tutorials
============

Ready to get your project up and running with Selenium WebDriver and Sauce
Labs? You've come to the right place. If you are new to Selenium, you might
want to jump down and have a look at the [complete tutorial](#complete).

We will be using [Maven](http://maven.apache.org) Java 6 and Junit 4 in this 
example. However Sauce Labs is framework agnostic and works great with any 
combination of tools you choose to write your tests in.

After we've set ourselves up, your tests will look something like this:
```java
//Example code here
    @Before
    public void setUp() throws Exception {
        this.caps.setCapability("name", this.testName.getMethodName());
        this.driver = new RemoteWebDriver(
                new URL("http://<username>:<key>@ondemand.saucelabs.com:80/wd/hub"),
                this.caps);
    }

    @Test
    public void checkRamenInventorMention() {
        driver.get("http://en.wikipedia.org/");

        WebElement searchInput = driver.findElement(By.id("searchInput"));
        searchInput.sendKeys("Ramen");
        
        WebElement button = driver.findElement(By.id("searchButton"));
        button.click();

        String source = driver.getPageSource();
        assertThat(source, containsString("Momofuku Ando"));
    }

    @After
    public void tearDown() throws Exception {
        driver.quit();
    }
```

Your tests are run in real browsers on a real operating system, in a dedicated, single-use VM.
Once they're complete, screenshots, video, Selenium log and a log of
passes and failures can be seen and shared.

We're working on making this tutorial as clear, simple, and relevant
as possible. If you run into any problems, or have questions or
suggestions, please don't hesitate to email help@saucelabs.com!

What You'll Need
----------------

You will want the dependencies section of your pom.xml to look something like this:
```xml
  <dependencies>
    ...
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.seleniumhq.selenium</groupId>
      <artifactId>selenium-remote-driver</artifactId>
      <version>2.32.0</version>
    </dependency>
    ...
  </dependencies>
```

Setting up Surefire
___________________
Run ```shell mvn archetype:generate``` to create your project.

<!-- SAUCE:INCLUDE:maven_setup -->

Run your first test suite
---
Now that Maven is installed and your archetype is created, let's run our tests:

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

        DesiredCapabilities capabilities = DesiredCapabilities.firefox();
        capabilities.setCapability("version", "5");
        capabilities.setCapability("platform", Platform.XP);
        this.driver = new RemoteWebDriver(
                new URL("http://<!-- SAUCE:USERNAME -->:<!-- SAUCE:ACCESS_KEY -->@ondemand.saucelabs.com:80/wd/hub"),
                capabilities);
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

        DesiredCapabilities capabilities = new DesiredCapabilities();
        capabilities.setBrowserName(browser);
        capabilities.setCapability("version", browserVersion);
        capabilities.setCapability("platform", Platform.valueOf(os));
        capabilities.setCapability("name", method.getName());
        this.driver = new RemoteWebDriver(
                new URL("http://" + username + ":" + key + "@ondemand.saucelabs.com:80/wd/hub"),
                capabilities);
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
