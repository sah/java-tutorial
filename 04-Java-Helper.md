Using the Java Helper library
====
The [Java helper library] adds some extra features to the running of tests using Sauce OnDemand, while only requiring minimal changes to the test class.  There are separate JUnit and TestNG versions of the Java helper library, which are included as dependencies in the Maven pom file when the archetype generation is performed.

**JUnit**

The Java helper libraries are included in the project by virtue of the following dependency in the pom.xml file:

```xml
<dependency>
    <groupId>com.saucelabs</groupId>
    <artifactId>sauce_junit</artifactId>
    <version>1.0.9</version>
    <scope>test</scope>
</dependency>
```

In addition to the WebDriver.java class, the Maven archetype will create a class which demonstrates how to update a test to use the Java helper library.  This class is located in the `src/test/java/com/yourcompany/WebDriverWithHelperTest.java` file:

```java
public class WebDriverWithHelperTest implements SauceOnDemandSessionIdProvider {

    public SauceOnDemandAuthentication authentication = new SauceOnDemandAuthentication("<your-user-name>", "<your-access-key>");

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
                new URL("http://" + authentication.getUsername() + ":" + authentication.getAccessKey() + "@ondemand.saucelabs.com:80/wd/hub"),
                capabillities);
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

The class implements the com.saucelabs.common.SauceOnDemandSessionIdProvider interface, which requires that a `getSessionId()` method
be implemented.

```java
public SauceOnDemandAuthentication authentication = new SauceOnDemandAuthentication("<your-user-name>", "<your-access-key>");

public @Rule
SauceOnDemandTestWatcher resultReportingTestWatcher = new SauceOnDemandTestWatcher(this, authentication);

```
The com.saucelabs.common.SauceOnDemandAuthentication instance will be created with your Sauce OnDemand user name and access key, which will 
in turn be used to create the com.saucelabs.junit.SauceOnDemandTestWatcher instance.

The SauceOnDemandTestWatcher instance will invoke the [Sauce REST API] to mark the Sauce Job as passed or failed if the JUnit test passes or fails.  It will also output the Sauce OnDemand session id to the stdout so that the [Sauce CI plugins] can parse the session id.

**TestNG**

The Java helper libraries are included in the project by virtue of the following dependency in the pom.xml file:

```xml
<dependency>
    <groupId>com.saucelabs</groupId>
    <artifactId>sauce_testng</artifactId>
    <version>1.0.9</version>
    <scope>test</scope>
</dependency>
```

As per the JUnit example, the TestNG Maven archetype will also create a class which demonstrates how to update a test to use the Java helper library.  This class is located in the `src/test/java/com/yourcompany/WebDriverWithHelperTest.java` file:

```java
@Listeners({SauceOnDemandTestListener.class})
public class WebDriverWithHelperTest implements SauceOnDemandSessionIdProvider, SauceOnDemandAuthenticationProvider {

    public SauceOnDemandAuthentication authentication;

    private WebDriver driver;

    @Parameters({"username", "key", "os", "browser", "browserVersion"})
    @BeforeMethod
    public void setUp(@Optional("${sauceUserName}") String username,
                      @Optional("${sauceAccessKey}") String key,
                      @Optional("mac") String os,
                      @Optional("iphone") String browser,
                      @Optional("5.0") String browserVersion,
                      Method method) throws Exception {

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
                new URL("http://" + authentication.getUsername() + ":" + authentication.getAccessKey() + "@ondemand.saucelabs.com:80/wd/hub"),
                capabillities);
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

This WebDriverWithHelperTest is fundamentally the same as the WebDriverTest class, with a couple of additions.

```java
@Listeners({SauceOnDemandTestListener.class})
public class WebDriverWithHelperTest implements SauceOnDemandSessionIdProvider, SauceOnDemandAuthenticationProvider {
```

The class is annotated with the org.testng.annotations.Listeners annotation, which includes the com.saucelabs.testng.SauceOnDemandTestListener class.  The SauceOnDemandTestListener class will invoke the [Sauce REST API] to mark the Sauce Job as passed or failed if the JUnit test passes or fails.  It will also output the Sauce OnDemand session id to the stdout so that the [Sauce CI plugins] can parse the session id.