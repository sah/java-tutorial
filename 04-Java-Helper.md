Using the Java Helper Library
====
The [Java helper library](https://github.com/saucelabs/sauce-java) provides additional test functionality when 
using Sauce (like pass/fail reporting), and it only requires minimal changes to the test class. There are 
JUnit and TestNG versions of the Java helper library. The version you are using is included as a dependency in the 
Maven pom file.

**JUnit**

To include the Java helper libraries in a JUnit project, add the following dependency to the pom.xml file (this was
already created by Maven for this tutorial):

```xml
<dependency>
    <groupId>com.saucelabs</groupId>
    <artifactId>sauce_junit</artifactId>
    <version><!-- SAUCE:PROP:sauce-java-version --></version>
    <scope>test</scope>
</dependency>
```

In addition to the WebDriver.java class, Maven creates the `WebDriverWithHelperTest` class that demonstrates how 
to update tests to use the Java helper library. You can find this class in the 
`src/test/java/com/yourcompany/WebDriverWithHelperTest.java` file shown below:

<!-- SAUCE:LOGIN -->
```java
public class WebDriverWithHelperTest implements SauceOnDemandSessionIdProvider {

    public SauceOnDemandAuthentication authentication = new SauceOnDemandAuthentication(
				"<!-- SAUCE:USERNAME -->", "<!-- SAUCE:ACCESS_KEY -->");

    /**
     * JUnit Rule that marks the Sauce Job as passed/failed when the test succeeds or fails.
     * You can see the pass/fail status on your [Sauce Labs test page](https://saucelabs.com/tests).
     */
    public @Rule
    SauceOnDemandTestWatcher resultReportingTestWatcher = new SauceOnDemandTestWatcher(this, authentication);

    /**
     * JUnit Rule that will record the test name of the current test. This is referenced when creating the 
     * {@link DesiredCapabilities}, so the Sauce Job is created with the test name.
     */
    public @Rule TestName testName = new TestName();

    private WebDriver driver;

    private String sessionId;

    @Before
    public void setUp() throws Exception {

        DesiredCapabilities capabilities = DesiredCapabilities.firefox();
        capabilities.setCapability("version", "17");
        // Note: XP is tested as Windows 2003 Server on the Sauce Cloud
        capabilities.setCapability("platform", Platform.XP); 
        capabilities.setCapability("name",  testName.getMethodName());
        this.driver = new RemoteWebDriver(
                new URL("http://" + authentication.getUsername() + ":" + authentication.getAccessKey() + "@ondemand.saucelabs.com:80/wd/hub"), capabilities);
        this.sessionId = ((RemoteWebDriver)driver).getSessionId().toString();
    }

    @Override
    public String getSessionId() {
        return sessionId;
    }

    @Test
    public void webDriverWithHelper() throws Exception {
        driver.get("http://www.amazon.com/");
        assertEquals("Amazon.com: Online Shopping for Electronics, Apparel, Computers, Books, DVDs & more", driver.getTitle());
    }

    @After
    public void tearDown() throws Exception {
        driver.quit();
    }

}
```

The `WebDriverWithHelperTest` class is fundamentally the same as the WebDriverTest class, with a couple of additions. 
First it implements the Sauce `SauceOnDemandSessionIdProvider` interface, which requires that a `getSessionId()` method 
be implemented:


```java
public class WebDriverWithHelperTest implements SauceOnDemandSessionIdProvider {
```

Pass your Sauce user name and Sauce access key as parameters to the `SauceOnDemandAuthentication` constructor. (You can find your 
Sauce access key on your [Sauce account page](https://saucelabs.com/account).) The 
object that is returned is passed as a parameter to the `SauceOnDemandTestWatcher` constructor. `SauceOnDemandTestWatcher` 
notifies Sauce if the test passed or failed.

```java
public SauceOnDemandAuthentication authentication = new SauceOnDemandAuthentication("<!-- SAUCE:USERNAME -->", "<!-- SAUCE:ACCESS_KEY -->");

public @Rule
SauceOnDemandTestWatcher resultReportingTestWatcher = new SauceOnDemandTestWatcher(this, authentication);

```

The SauceOnDemandTestWatcher instance invokes the [Sauce REST API](http://saucelabs.com/docs/rest). This is how JUnit
notifies the Sauce environment if the test passed or failed. It also outputs the Sauce session id 
to stdout so the Sauce plugins for [Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Sauce+OnDemand+Plugin) 
and [Bamboo](https://marketplace.atlassian.com/plugins/com.saucelabs.bamboo.bamboo-sauceondemand-plugin) 
can parse the session id.

**TestNG**

To include the Java helper libraries in a TestNG project, add the following dependency to the pom.xml file (this 
was automatically created by Maven for this tutorial):

```xml
<dependency>
    <groupId>com.saucelabs</groupId>
    <artifactId>sauce_testng</artifactId>
    <version><!-- SAUCE:PROP:sauce-java-version --></version>
    <scope>test</scope>
</dependency>
```

As with the JUnit example, the TestNG Maven archetype creates a `WebDriverWithHelperTest` class that demonstrates 
how to update tests to use the Java helper library.  This class is located in the 
`src/test/java/com/yourcompany/WebDriverWithHelperTest.java` file shown below:

```java
@Listeners({SauceOnDemandTestListener.class})
public class WebDriverWithHelperTest implements SauceOnDemandSessionIdProvider, SauceOnDemandAuthenticationProvider {

    public SauceOnDemandAuthentication authentication;

    private WebDriver driver;

    @Parameters({"username", "key", "os", "browser", "browserVersion"})
    @BeforeMethod
    // Note: XP is tested as Windows 2003 Server on the Sauce Cloud
    public void setUp
        (@Optional("<!-- SAUCE:USERNAME -->") String username,
            @Optional("<!-- SAUCE:ACCESS_KEY -->") String key,
            @Optional("XP") String os,
            @Optional("firefox") String browser,
            @Optional("17") String browserVersion, Method method) throws Exception {

        if (StringUtils.isNotEmpty(username) && StringUtils.isNotEmpty(key)) {
            authentication = new SauceOnDemandAuthentication(username, key);
        } else {
            authentication = new SauceOnDemandAuthentication();
        }

        DesiredCapabilities capabilities = new DesiredCapabilities();
        capabilities.setBrowserName(browser);
        capabilities.setCapability("version", browserVersion);
        capabilities.setCapability("platform", Platform.valueOf(os));
        capabilities.setCapability("name", method.getName());
        this.driver = new RemoteWebDriver(
            new URL("http://" + authentication.getUsername() + ":" + authentication.getAccessKey() + "@ondemand.saucelabs.com:80/wd/hub"), capabilities);
    }

    @Override
    public String getSessionId() {
        SessionId sessionId = ((RemoteWebDriver)driver).getSessionId();
        return (sessionId == null) ? null : sessionId.toString();
    }

    @Test
    public void webDriverWithHelper() throws Exception {
        driver.get("http://www.amazon.com/");
        assertEquals("Amazon.com: Online Shopping for Electronics, Apparel, Computers, Books, DVDs & more", driver.getTitle());
    }

    @AfterMethod
    public void tearDown() throws Exception {
        driver.quit();
    }

    @Override
    public SauceOnDemandAuthentication getAuthentication() {
        return authentication;
    }
}
```

This WebDriverWithHelperTest class is fundamentally the same as the WebDriverTest class, with a couple of additions. It 
is annotated with the TestNG annotation `@Listeners`, which includes the 
`SauceOnDemandTestListener` class. The SauceOnDemandTestListener class invokes 
the [Sauce REST API](http://saucelabs.com/docs/rest), which notifies Sauce if the test passed or failed. 
It also outputs the Sauce session id to stdout so the Sauce plugins 
for [Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Sauce+OnDemand+Plugin) 
and [Bamboo](https://marketplace.atlassian.com/plugins/com.saucelabs.bamboo.bamboo-sauceondemand-plugin) 
can parse the session id.


* _Next_: [Running tests against web applications](##04-Testing-Apps.md##)
