What You'll Need
----

- Java 6 or greater
- [Maven](http://maven.apache.org)
- JUnit or TestNG

([Here's how you can get these set up](##02-Setup.md##).)


2. Setting Up a Project
----

Let's start by creating a project directory for this tutorial:

```bash
$ mkdir ~/sauce-tutorial
$ cd ~/sauce-tutorial
```

Next, download a sample project using your chosen testing
framework using one of these Maven commands.
You will be prompted to enter a group id (for example,
`com.yourcompany`), artifact id (for example, `sauce-project`),
version (defaults to `1.0.0-SNAPSHOT`), and package (default to the
group id).

**JUnit**

```bash
$ mvn archetype:generate -DarchetypeRepository=http://repository-saucelabs.forge.cloudbees.com/release -DarchetypeGroupId=com.saucelabs -DarchetypeArtifactId=quickstart-webdriver-junit -DarchetypeVersion=<!-- SAUCE:PROP:sauce-java-version --> -DsauceUserName=<!-- SAUCE:USERNAME --> -DsauceAccessKey=<!-- SAUCE:ACCESS_KEY -->
```

**TestNG**

```bash
$ mvn archetype:generate -DarchetypeRepository=http://repository-saucelabs.forge.cloudbees.com/release -DarchetypeGroupId=com.saucelabs -DarchetypeArtifactId=quickstart-webdriver-testng -DarchetypeVersion=<!-- SAUCE:PROP:sauce-java-version --> -DsauceUserName=<!-- SAUCE:USERNAME --> -DsauceAccessKey=<!-- SAUCE:ACCESS_KEY -->
```

Once the command finishes, the sample project files should have been
created in the `~/sauce-tutorial/sauce-project`
directory. Change to the project directory so you will be ready to run
the tests:

```bash
$ cd sauce-project
```

3. Writing tests
---

The sample project includes some example tests in the directory
`src/test/java/com/yourcompany/`. Lets look at the simplest test in
`WebDriverTest.java` to see how it works.

The `setUp()` method initializes the browser testing environment by specifying the
browser, version, and platform to test, then creates a
`RemoteWebDriver` to run the tests remotely:

```java
    public void setUp(...) throws Exception {
        DesiredCapabilities capabilities = new DesiredCapabilities();
        capabilities.setBrowserName(browser);
        capabilities.setCapability("version", browserVersion);
        capabilities.setCapability("platform", os);
        capabilities.setCapability("name", method.getName());
        this.driver = new RemoteWebDriver(
                new URL("http://" + username + ":" + key + "@ondemand.saucelabs.com:80/wd/hub"),
                capabilities);
    }
```

The `RemoteWebDriver` is a [standard Selenium
interface](http://selenium.googlecode.com/git/docs/api/java/index.html?org/openqa/selenium/remote/RemoteWebDriver.html),
so you can do anything that you could do with a
local Selenium test. The only code specific to Sauce Labs was the URL
that makes the test run using a browser on Sauce Labs' servers. 

Once connected to Sauce Labs, the test runs commands to remote-control a browser. This simple example test simply requests a page and makes one assertion. It runs against several browsers simultaneously, to demonstrate parallelized testing, and reports its status to Sauce Labs when complete. 

4. Running tests
---

Run this command from your `sauce-project` directory:

    mvn test

This launches Maven and will download the dependencies, compile the source code and run the tests. After a few
moments you should see that JUnit/TestNG has started. Eventually you will see the following output:

	------------------------------------------------------
	 T E S T S
	-------------------------------------------------------
	Running WebDriverTest
	Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 14.384 sec
	Running WebDriverWithHelperTest
	Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 14.743 sec

	Results :

	Tests run: 2, Failures: 0, Errors: 0, Skipped: 0

(The exact output will depend on the test framework you chose, but you
should see all tests passing.)
