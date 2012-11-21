Using the Java Helper Library
====
The [Java helper library](https://github.com/saucelabs/sauce-java) provides additional test functionality when 
using Sauce OnDemand (like pass/fail reporting), and it only requires minimal changes to the test class. There are 
JUnit and TestNG versions of the Java helper library. You include the version you are using as a dependency in the 
Maven pom file.

**JUnit**

To include the Java helper libraries in a JUnit project, add the following dependency to the pom.xml file:

```xml
<dependency>
    <groupId>com.saucelabs</groupId>
    <artifactId>sauce_junit</artifactId>
    <version>1.0.10</version>
    <scope>test</scope>
</dependency>
```

In addition to the WebDriver.java class, Maven creates a class that demonstrates how to update 
tests to use the Java helper library. You can find this class in the 
`src/test/java/com/yourcompany/WebDriverWithHelperTest.java` file:

<!-- SAUCE:LOGIN -->
```java
public class WebDriverWithHelperTest implements SauceOnDemandSessionIdProvider {

    public SauceOnDemandAuthentication authentication = new SauceOnDemandAuthentication(
				"<!-- SAUCE:USERNAME -->", "<!-- SAUCE:ACCESS_KEY -->");

    public @Rule
    SauceOnDemandTestWatcher resultReportingTestWatcher = new SauceOnDemandTestWatcher(this, authentication);

    public @Rule TestName testName= new TestName();

    private WebDriver driver;

    private String sessionId;

    @Before
    public void setUp() throws Exception {

        DesiredCapabilities capabillities = DesiredCapabilities.firefox();
        capabillities.setCapability("version", "5");
        capabillities.setCapability("platform", Platform.XP);
        capabillities.setCapability("name",  testName.getMethodName());
        this.driver = new RemoteWebDriver(
                new URL("http://" + authentication.getUsername() + ":" + authentication.getAccessKey() + "@ondemand.saucelabs.com:80/wd/hub"), capabillities);
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

This WebDriverWithHelperTest is fundamentally the same as the WebDriverTest class, with a couple of additions.

```java
public class WebDriverWithHelperTest implements SauceOnDemandSessionIdProvider {
```

The WebDriverWithHelperTest fclass implements the `com.saucelabs.common.SauceOnDemandSessionIdProvider` interface, 
which requires that a `getSessionId()` method be implemented.

```java
public SauceOnDemandAuthentication authentication = new SauceOnDemandAuthentication("<!-- SAUCE:USERNAME -->", "<!-- SAUCE:ACCESS_KEY -->");

public @Rule
SauceOnDemandTestWatcher resultReportingTestWatcher = new SauceOnDemandTestWatcher(this, authentication);

```

Your Sauce OnDemand user name and Sauce access key are passed as parameters to the 
`com.saucelabs.common.SauceOnDemandAuthentication` constructor. The reference that is returned is passed 
as a parameter to the `com.saucelabs.junit.SauceOnDemandTestWatcher` constructor. (You can find your 
Sauce access key on your [Sauce account page](https://saucelabs.com/account).)

The SauceOnDemandTestWatcher instance invokes the [Sauce REST API](http://saucelabs.com/docs/rest) to identify the 
Sauce job as passed or failed if the JUnit test passes or fails. It also outputs the Sauce OnDemand session id 
to stdout so the Sauce plugins for [Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Sauce+OnDemand+Plugin) 
and [Bamboo](https://marketplace.atlassian.com/plugins/com.saucelabs.bamboo.bamboo-sauceondemand-plugin) 
can parse the session id.

**TestNG**

To include the Java helper libraries in a TestNG project, add the following dependency to the pom.xml file:

```xml
<dependency>
    <groupId>com.saucelabs</groupId>
    <artifactId>sauce_testng</artifactId>
    <version>1.0.10</version>
    <scope>test</scope>
</dependency>
```

As with the JUnit example, the TestNG Maven archetype creates a class that demonstrates how to update tests 
to use the Java helper library.  This class is located in the 
`src/test/java/com/yourcompany/WebDriverWithHelperTest.java` file:

```java
@Listeners({SauceOnDemandTestListener.class})
public class WebDriverWithHelperTest implements SauceOnDemandSessionIdProvider, SauceOnDemandAuthenticationProvider {

    public SauceOnDemandAuthentication authentication;

    private WebDriver driver;

    @Parameters({"username", "key", "os", "browser", "browserVersion"})
    @BeforeMethod
    public void setUp
        (@Optional("<!-- SAUCE:USERNAME -->") String username,
              @Optional("<!-- SAUCE:ACCESS_KEY -->") String key,
              @Optional("mac") String os,
              @Optional("iphone") String browser,
              @Optional("5.0") String browserVersion, Method method) throws Exception {

        if (StringUtils.isNotEmpty(username) && StringUtils.isNotEmpty(key)) {
            authentication = new SauceOnDemandAuthentication(username, key);
        } else {
            authentication = new SauceOnDemandAuthentication();
        }

        DesiredCapabilities capabillities = new DesiredCapabilities();
        capabillities.setBrowserName(browser);
        capabillities.setCapability("version", browserVersion);
        capabillities.setCapability("platform", Platform.valueOf(os));
        capabillities.setCapability("name", method.getName());
        this.driver = new RemoteWebDriver(
            new URL("http://" + authentication.getUsername() + ":" + authentication.getAccessKey() + "@ondemand.saucelabs.com:80/wd/hub"), capabillities);
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

This WebDriverWithHelperTest class is fundamentally the same as the WebDriverTest class, with a couple of additions:

```java
@Listeners({SauceOnDemandTestListener.class})
public class WebDriverWithHelperTest implements SauceOnDemandSessionIdProvider, SauceOnDemandAuthenticationProvider {
```

The WebDriverWithHelperTest class is annotated with the `org.testng.annotations.Listeners` annotation `@Listeners`, which includes the 
`com.saucelabs.testng.SauceOnDemandTestListener` class. The SauceOnDemandTestListener class invokes 
the [Sauce REST API](http://saucelabs.com/docs/rest) which marks the Sauce job as passed or failed if the test 
passes or fails.  It also outputs the Sauce OnDemand session id to stdout so the Sauce plugins 
for [Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Sauce+OnDemand+Plugin) 
and [Bamboo](https://marketplace.atlassian.com/plugins/com.saucelabs.bamboo.bamboo-sauceondemand-plugin) 
can parse the session id.

* _Next_: [Running tests against web applications](##04-Testing-Apps.md##)
