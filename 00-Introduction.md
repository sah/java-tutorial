Using Sauce Labs with Java
============

Sauce Labs lets you easily run Selenium tests against a wide range of
browsers on Windows, OS X, Linux, iOS and Android. Your tests are run
in real browsers on a real operating system, in a dedicated,
single-use VM. Once they're complete, screenshots, video, Selenium log
and a log of passes and failures can be seen and shared.

Ready to get your project up and running with Selenium WebDriver and Sauce
Labs? You've come to the right place.


What You'll Need
----

This tutorial assumes you have a recent (&gt;=Java 6) version of Java
and are using [Maven](http://maven.apache.org) to build and deploy
your Java projects. It also assumes you are using JUnit or TestNG for
testing.

If you do not have these installed and configured, follow these
instructions to [setup the requirements](##09-Requirements.md##).


Setting Up a Project
----

Let's start by creating a project directory for this tutorial:

<!-- SAUCE:BEGIN_PLATFORM:MAC|LINUX -->
<a id="maven_mac"></a><a id="maven_linux"></a>
```bash
user@host ~ $ mkdir ~/sauce-tutorial && cd ~/sauce-tutorial
```
<!-- SAUCE:END_PLATFORM -->
<!-- SAUCE:BEGIN_PLATFORM:WIN -->
<a id="maven_win"></a>

    C:\> mkdir C:\sauce-tutorial
    C:\> cd C:\sauce-tutorial
<!-- SAUCE:END_PLATFORM -->

Next, download and install a sample project using your chosen testing
framework using one of these Maven commands.
You will be prompted to enter a group id (for example,
`com.yourcompany`), artifact id (for example, `sauce-project`),
version (defaults to `1.0.0-SNAPSHOT`), and package (default to the
group id).

**Note** This step uses
your Sauce username and access key. You can find your Sauce access key
on your [Sauce account page](https://saucelabs.com/account). If you
are logged in, it will be automatically included in the code snippets
below.

<!-- SAUCE:LOGIN -->

<!-- SAUCE:BEGIN_PLATFORM:MAC|LINUX -->
**JUnit**

```bash
user@host ~/sauce-tutorial $ mvn archetype:generate -DarchetypeRepository=http://repository-saucelabs.forge.cloudbees.com/release -DarchetypeGroupId=com.saucelabs -DarchetypeArtifactId=quickstart-webdriver-junit -DarchetypeVersion=<!-- SAUCE:PROP:sauce-java-version --> -DsauceUserName=<!-- SAUCE:USERNAME --> -DsauceAccessKey=<!-- SAUCE:ACCESS_KEY -->
```

**TestNG**

```bash
user@host ~/sauce-tutorial $ mvn archetype:generate -DarchetypeRepository=http://repository-saucelabs.forge.cloudbees.com/release -DarchetypeGroupId=com.saucelabs -DarchetypeArtifactId=quickstart-webdriver-testng -DarchetypeVersion=<!-- SAUCE:PROP:sauce-java-version --> -DsauceUserName=<!-- SAUCE:USERNAME --> -DsauceAccessKey=<!-- SAUCE:ACCESS_KEY -->
```

<!-- SAUCE:END_PLATFORM -->
<!-- SAUCE:BEGIN_PLATFORM:WIN -->
**JUnit**

	C:\sauce-tutorial> mvn archetype:generate -DarchetypeRepository=http://repository-saucelabs.forge.cloudbees.com/release -DarchetypeGroupId=com.saucelabs -DarchetypeArtifactId=quickstart-webdriver-junit -DarchetypeVersion=<!-- SAUCE:PROP:sauce-java-version --> -DsauceUserName=<!-- SAUCE:USERNAME --> -DsauceAccessKey=<!-- SAUCE:ACCESS_KEY -->

**TestNG**

	C:\sauce-tutorial> mvn archetype:generate -DarchetypeRepository=http://repository-saucelabs.forge.cloudbees.com/release -DarchetypeGroupId=com.saucelabs -DarchetypeArtifactId=quickstart-webdriver-testng -DarchetypeVersion=<!-- SAUCE:PROP:sauce-java-version --> -DsauceUserName=<!-- SAUCE:USERNAME --> -DsauceAccessKey=<!-- SAUCE:ACCESS_KEY -->

<!-- SAUCE:END_PLATFORM -->

Once the command finishes, the sample project files should have been
created in the `~/sauce-tutorial/sauce-project`
directory. Change to the project directory so you will be ready to run
the tests:

<!-- SAUCE:BEGIN_PLATFORM:MAC|LINUX -->
```bash
user@host ~/sauce-tutorial $ cd sauce-project
```
<!-- SAUCE:END_PLATFORM -->

<!-- SAUCE:BEGIN_PLATFORM:WIN -->
    C:\sauce-tutorial> cd sauce-project
<!-- SAUCE:END_PLATFORM -->


Writing your tests
---

Thankfully, the sample project has done all the work of writing tests
for you! The sample tests are in the directory
`src/test/java/com/yourcompany/`. Lets look at the simplest test in
`WebDriverTest.java` to see how we set up a test suite.

This test primarily demonstrates the setup and teardown process. The
`setUp` method initializes the browser testing environment by specifying the
browser, version, and platform to test, then creates a
`RemoteWebDriver` to run the tests remotely. The test simply requests a
page and makes one assertion.

**JUnit**

```java
public class WebDriverTest {

    private WebDriver driver;

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

    @Test
    public void webDriver() throws Exception {
        // Make the browser get the page and check its title
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

        // Choose the browser, version, and platform to test
        DesiredCapabilities capabilities = new DesiredCapabilities();
        capabilities.setBrowserName(browser);
        capabilities.setCapability("version", browserVersion);
        capabilities.setCapability("platform", Platform.valueOf(os));
        capabilities.setCapability("name", method.getName());
        // Create the connection to Sauce Labs to run the tests
        this.driver = new RemoteWebDriver(
                new URL("http://" + username + ":" + key + "@ondemand.saucelabs.com:80/wd/hub"),
                capabilities);
    }

    @Test
    public void webDriver() throws Exception {
        // Make the browser get the page and check its title
        driver.get("http://www.amazon.com/");
        assertEquals("Amazon.com: Online Shopping for Electronics, Apparel, Computers, Books, DVDs & more", driver.getTitle());
    }

    @AfterMethod
    public void tearDown() throws Exception {
        driver.quit();
    }

}
```

And that's it! The [Java Helper](##04-Java-Helper.md##) section of the
tutorial explains more Sauce functionality you can use in your
tests, but let's try running this simple test first.


Running your tests
---

<!-- SAUCE:INCLUDE:run_maven -->

While the tests are running head to your [Sauce Labs account
page](https://saucelabs.com/account) and you can see them running.
You can even watch them running live if you want by clicking the test names!


Next steps
---
<a name="complete"></a>
Congratulations, you just ran your first Selenium WebDriver tests on
the Sauce Labs cloud! So what's next?

### More Detailed Setup

If this tutorial went by a little too quickly, follow these more
detailed steps to understand how Selenium and Sauce Labs work and get
your first test up and running:

* [How Selenium WebDriver and Sauce Labs work](##01-Selenium.md##)
* [Setting up your system to use Sauce with Java](##02-Setup.md##)
* [Running your first test](##03-First-Test.md##)

### Sauce Labs Features

Ready to start implementing more advanced tests? Follow these steps to
learn more about Sauce Labs testing:

* [Running tests against web applications](##04-Testing-Apps.md##)
* [Using the Sauce Java Helper libraries](##04-Java-Helper.md##)
* [Testing local apps with Sauce Connect](##05-Sauce-Connect.md##)
* [Running tests in parallel](##06-Parallelism.md##)
* [Tips for better Selenium test performance](##07-Tips.md##)

### Other Resources

Looking for more information than this tutorial? Links to more
information about Selenium, automated functional testing, and Sauce
Labs features can be found on the [pointers page](##08-Info.md##) of
this tutorial.
