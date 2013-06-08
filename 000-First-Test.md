Sauce Labs, Selenium WebDriver, and Java Tutorials
============

Ready to get your project up and running with Selenium WebDriver and Sauce
Labs? You've come to the right place.

We will be using [Maven](http://maven.apache.org), [Java 6]() and [Junit 4]() in this 
example. However Sauce Labs is framework agnostic and works great with any 
combination of tools and frameworks you choose to write your tests in.

After we've set ourselves up, your tests will look something like this:
```java
/*
 * Work in progress
*/
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


Getting started
---

Let us start by running the following command.

```shell 
$ mvn archetype:generate
```

Feel free to use your own lovingly crafted archetype, we will be using the default archetype 
in this example. 

Then tis time to delve into harsh beauty of xml. Open up the project's pom.xml in your 
favorite editor and make the following amendments if necessary.

Firstly, you will want the dependencies section of your pom.xml to look something like this:
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

And the build section should be similar to this:
```xml
<build>    
  ...
  <plugins>
    ...
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-surefire-plugin</artifactId>
      <version>2.14.1</version>
      <configuration>
	<parallel>both</parallel>
	<threadCount>10</threadCount>
      </configuration>
    </plugin>
    ...
  </plugins>
  ...
</build>
```

Setting up your environment
---

<!-- SAUCE:LOGIN -->

We recomend keeping your Sauce Labs credentials as environment variables. No more checking in your credentials by error!

<!-- SAUCE:BEGIN_PLATFORM:MAC|LINUX -->
To do this open the configuration file for your shell (`~/.bash_profile` for bash) and add the following lines:

```bash
export SAUCE_USERNAME=<!-- SAUCE:USERNAME -->
export SAUCE_ACCESS_KEY=<!-- SAUCE:ACCESS_KEY -->
```

After that re-load the configuration by `source`-ing the configuration file (`source ~/.bash_profile` for bash). 
<!-- SAUCE:END_PLATFORM --> 

<!-- SAUCE:BEGIN_PLATFORM:WIN|WINDOWS -->
Open your environment variables settings window (Instructions [here](http://www.itechtalk.com/thread3595.html)) and set the following variables:
```
Name: SAUCE_USERNAME
Value: <!-- SAUCE:USERNAME -->

Name: SAUCE_ACCESS_KEY
Value:  <!-- SAUCE:ACCESS_KEY -->
```
<!-- SAUCE:END_PLATFORM --> 

Writting your tests
---
Yea! Our setup is finished, now to test writting!

First to set up our target os/browser combos (did I just say no more setting up?) 
```java
@Parameterized.Parameters()
public static Collection<Object[]> data() {
    LinkedList<Object[]> data = new LinkedList<Object[]>();

    DesiredCapabilities capabilities = DesiredCapabilities.firefox();
    capabilities.setCapability("version", "21");
    capabilities.setCapability("platform", Platform.XP);
    data.add(new Object[]{capabilities});

    capabilities = DesiredCapabilities.firefox();
    capabilities.setCapability("version", "21");
    capabilities.setCapability("platform", Platform.LINUX);
    data.add(new Object[] {capabilities} );

    capabilities = DesiredCapabilities.firefox();
    capabilities.setCapability("version", "21");
    capabilities.setCapability("platform", Platform.MAC);
    data.add(new Object[] {capabilities} );

    return data;
}
```

```java
public SauceOnDemandAuthentication auth = new SauceOnDemandAuthentication();
@Rule public TestName testName = new TestName();

...

@Before
public void setUp() throws Exception {
    this.caps.setCapability("name", this.testName.getMethodName());
    this.driver = new RemoteWebDriver(
		      new URL("http://"+auth.getUsername()+":"+auth.getAccessKey()+"@ondemand.saucelabs.com:80/wd/hub"),
					      this.caps);
}
```

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
*   We tell Sauce the browser and platform we want to use by setting . (To see all
    the different possibilities, check out Sauce's
    [browsers](https://saucelabs.com/docs/browsers) page).

And that's all we need to do to get the test suite running on Sauce! 

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

