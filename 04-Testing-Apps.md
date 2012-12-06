Running Tests Against Web Applications
====

Testing a static sandbox is one thing. Testing a real application's functionality
is another. In this tutorial we'll run Selenium tests against a real live app sitting
on the web to test signup, login and logout 
behaviours. Once we're done you'll have a good idea how to write Selenium
tests for signup and login and you'll have a general understanding
of how to test other site functionality as well.

The Test App
---

We have a [demo app](http://tutorialapp.saucelabs.com) set up that we can run 
Selenium scripts against. It's a web-based "idea competition" demo app called 
[Shootout](https://github.com/Pylons/shootout). Shootout is a voting platform for ideas designed for the Pyramid Python 
web framework. We won't test voting functionality in this demo, but feel free to play around with it.

The Test Class
---

A `src/test/java/com/yourcompany/WebDriverDemoShootout.java` file was created in your `sauce-tutorial` directory by 
Maven. It's reproduced below. We ran these 8 tests previously
and you can view them in your [Sauce Labs tests page](https://saucelabs.com/tests).

<!-- SAUCE:LOGIN -->
```java
public class WebDriverDemoShootoutTest {

    private SauceOnDemandAuthentication authentication = new SauceOnDemandAuthentication("<!-- SAUCE:USERNAME -->", "<!-- SAUCE:ACCESS_KEY -->");

    private WebDriver driver;

    @Before
    public void setUp() throws Exception {
        DesiredCapabilities capabilities = DesiredCapabilities.firefox();
        capabilities.setCapability("version", "17");
        capabilities.setCapability("platform", Platform.XP);
        this.driver = new RemoteWebDriver(
                new URL("http://" + authentication.getUsername() + ":" + authentication.getAccessKey() + "@ondemand.saucelabs.com:80/wd/hub"),
                capabilities);
        driver.get("http://tutorialapp.saucelabs.com");
    }

    @After
    public void tearDown() throws Exception {
        driver.quit();
    }

    @Test
    public void testLoginFailsWithBadCredentials() throws Exception {
        String userName = getUniqueId();
        String password = getUniqueId();
        driver.findElement(By.name("login")).sendKeys(userName);
        driver.findElement(By.name("password")).sendKeys(password);
        driver.findElement(By.cssSelector("input.login")).click();
        assertNotNull("Text not found", driver.findElement(By.id("message")));
    }

    @Test
    public void testLogout() throws Exception {
        Map<String, String> userDetails = createRandomUser();
        doRegister(userDetails, true);
    }

    @Test
    public void testLogin() throws Exception {
        Map<String, String> userDetails = createRandomUser();
        doRegister(userDetails, true);
        doLogin(userDetails.get("username"), userDetails.get("password"));
    }

    @Test
    public void testRegister() throws Exception {
        Map<String, String> userDetails = createRandomUser();
        doRegister(userDetails, false);
        assertTrue("Message not found", driver.findElement(By.cssSelector(".username")).getText().contains("You are logged in as "));
    }

    @Test
    public void testRegisterFailsWithoutUsername() throws Exception {
        Map<String, String> userDetails = createRandomUser();
        userDetails.put("username", "");
        doRegister(userDetails, false);
        assertEquals("Message not found", "Please enter a value", driver.findElement(By.cssSelector(".error")).getText());

    }

    @Test
    public void testRegisterFailsWithoutName() throws Exception {
        Map<String, String> userDetails = createRandomUser();
        userDetails.put("name", "");
        doRegister(userDetails, false);
        assertEquals("Message not found", "Please enter a value", driver.findElement(By.cssSelector(".error")).getText());
    }

    @Test
    public void testRegisterFailsWithMismatchedPasswords() throws Exception {
        Map<String, String> userDetails = createRandomUser();
        userDetails.put("confirm_password", getUniqueId());
        doRegister(userDetails, false);
        assertEquals("Message not found", "Fields do not match", driver.findElement(By.cssSelector(".error")).getText());
    }

    @Test
    public void testRegisterFailsWithBadEmail() throws Exception {
        Map<String, String> userDetails = createRandomUser();
        userDetails.put("email", "test");
        doRegister(userDetails, false);
        assertEquals("Message not found", "An email address must contain a single @", driver.findElement(By.cssSelector(".error")).getText());
        driver.findElement(By.id("email")).clear();
        driver.findElement(By.id("email")).sendKeys("@example.com");
        driver.findElement(By.id("form.submitted")).click();
        assertEquals("Message not found", "The username portion of the email address is invalid (the portion before the @: )", driver.findElement(By.cssSelector(".error")).getText());
        driver.findElement(By.id("email")).clear();
        driver.findElement(By.id("email")).sendKeys("test@example");
        driver.findElement(By.id("form.submitted")).click();
        assertEquals("Message not found", "The domain portion of the email address is invalid (the portion after the @: bob)", driver.findElement(By.cssSelector(".error")).getText());
    }

    private String getUniqueId() {
        return Long.toHexString(Double.doubleToLongBits(Math.random()));
    }

    private void doRegister(Map<String, String> userDetails, boolean logout) {
        userDetails.put("confirm_password", userDetails.get("confirm_password") != null ?
                userDetails.get("confirm_password") : userDetails.get("password"));
        driver.get("http://tutorialapp.saucelabs.com/register");
        driver.findElement(By.id("username")).sendKeys(userDetails.get("username"));
        driver.findElement(By.id("password")).sendKeys(userDetails.get("password"));
        driver.findElement(By.id("confirm_password")).sendKeys(userDetails.get("confirm_password"));
        driver.findElement(By.id("name")).sendKeys(userDetails.get("name"));
        driver.findElement(By.id("email")).sendKeys(userDetails.get("email"));
        driver.findElement(By.id("form.submitted")).click();

        if (logout) {
            doLogout();
        }
    }

    private void doLogout() {
        driver.get("http://tutorialapp.saucelabs.com/logout");
        assertEquals("Message not found", "Logged out successfully.", driver.findElement(By.id("message")).getText());
    }

    private Map<String, String> createRandomUser() {
        Map<String, String> userDetails = new HashMap<String, String>();
        String fakeId = getUniqueId();
        userDetails.put("username", fakeId);
        userDetails.put("password", "testpass");
        userDetails.put("name", "Fake " + fakeId);
        userDetails.put("email", fakeId + "@example.com");
        return userDetails;
    }

    private void doLogin(String username, String password) {
        driver.findElement(By.name("login")).sendKeys(username);
        driver.findElement(By.name("password")).sendKeys(password);
        driver.findElement(By.cssSelector("input.login")).click();
        assertEquals("Message not found", "Logged in successfully.", driver.findElement(By.id("message")).getText());
    }

}
```

You're probably already familiar with a lot of what's going on in this test suite. 
Let's just take a quick look at some new commands and concepts.


The `createRandomUser()` function generates unique random user details for the registration
and login tests. The randomness is important because it allows our tests to
run in parallel as many times as we want without fear of collisions.

```java
private Map<String, String> createRandomUser() {
    Map<String, String> userDetails = new HashMap<String, String>();
    String fakeId = getUniqueId();
    userDetails.put("username", fakeId);
    userDetails.put("password", "testpass");
    userDetails.put("name", "Fake " + fakeId);
    userDetails.put("email", fakeId + "@example.com");
    return userDetails;
}
```

The `doRegister`, `doLogin` and `doLogout` methods are helper functions for our tests. If we were testing
a local app, it would be more efficient to perform these actions on the
server-side than to have Selenium generate states of being logged in, logged out,
etc. However, since we're testing an app on the Internet whose backend is inaccessible
to us, we're using these Selenium commands to put the user into these states.

```java
private void doRegister(Map<String, String> userDetails, boolean logout) {
       userDetails.put("confirm_password", userDetails.get("confirm_password") != null ?
               userDetails.get("confirm_password") : userDetails.get("password"));
       driver.get("http://tutorialapp.saucelabs.com/register");
       driver.findElement(By.id("username")).sendKeys(userDetails.get("username"));
       driver.findElement(By.id("password")).sendKeys(userDetails.get("password"));
       driver.findElement(By.id("confirm_password")).sendKeys(userDetails.get("confirm_password"));
       driver.findElement(By.id("name")).sendKeys(userDetails.get("name"));
       driver.findElement(By.id("email")).sendKeys(userDetails.get("email"));
       driver.findElement(By.id("form.submitted")).click();

       if (logout) {
           doLogout();
       }
   }

   private void doLogout() {
       driver.get("http://tutorialapp.saucelabs.com/logout");
       assertEquals("Message not found", "Logged out successfully.", driver.findElement(By.id("message")).getText());
   }

   private void doLogin(String username, String password) {
    	driver.findElement(By.name("login")).sendKeys(username);
	    driver.findElement(By.name("password")).sendKeys(password);
	    driver.findElement(By.cssSelector("input.login")).click();
	    assertEquals("Message not found", "Logged in successfully.", driver.findElement(By.id("message")).getText());
}
```

In our first test we make sure that logging in doesn't work with bad username/password
values by asserting that we get a login failure message when we put in random
text.

```java
@Test
public void testLoginFailsWithBadCredentials() throws Exception {
    String userName = getUniqueId();
    String password = getUniqueId();
    driver.findElement(By.name("login")).sendKeys(userName);
    driver.findElement(By.name("password")).sendKeys(password);
    driver.findElement(By.cssSelector("input.login")).click();
    assertNotNull("Text not found", driver.findElement(By.id("message")));
}
```

Next we test login and logout functionality. The first test creates a new user
and uses the `doLogout()` helper method to assert that the logout message
appears. The second test creates a random user, logs it out, and then uses
the `doLogin()` helper method to assert that the successful login message
appears.

```java
@Test
public void testLogin() throws Exception {
    Map<String, String> userDetails = createRandomUser();
    doRegister(userDetails, true);
    doLogin(userDetails.get("username"), userDetails.get("password"));
}

@Test
public void testLogout() throws Exception {
    Map<String, String> userDetails = createRandomUser();
    doRegister(userDetails, true);
}
```

Then we test Shootout's signup functionality by using the
registration helper function to create a new user and assert that the
user is logged in (which happens after a successful registration).

```java
@Test
public void testRegister() throws Exception {
    Map<String, String> userDetails = createRandomUser();
    doRegister(userDetails, false);
    assertTrue("Message not found", driver.findElement(By.cssSelector(".username")).getText().contains("You are logged in as "));
}
```

And finally we have a set of tests for validation logic in the signup form. First we test that each of the required 
fields results in an error on signup if the field is empty. Next we test if a mismatched password and password
confirmation generate the desired error, and then we test to make sure that the app doesn't allow a successful 
registration if various incorrect email formats are used.

```java
@Test
public void testRegisterFailsWithoutUsername() throws Exception {
    Map<String, String> userDetails = createRandomUser();
    userDetails.put("username", "");
    doRegister(userDetails, false);
    assertEquals("Message not found", "Please enter a value", driver.findElement(By.cssSelector(".error")).getText());

}

@Test
public void testRegisterFailsWithoutName() throws Exception {
    Map<String, String> userDetails = createRandomUser();
    userDetails.put("name", "");
    doRegister(userDetails, false);
    assertEquals("Message not found", "Please enter a value", driver.findElement(By.cssSelector(".error")).getText());
}

@Test
public void testRegisterFailsWithMismatchedPasswords() throws Exception {
    Map<String, String> userDetails = createRandomUser();
    userDetails.put("confirm_password", getUniqueId());
    doRegister(userDetails, false);
    assertEquals("Message not found", "Fields do not match", driver.findElement(By.cssSelector(".error")).getText());
}

@Test
public void testRegisterFailsWithBadEmail() throws Exception {
    Map<String, String> userDetails = createRandomUser();
    userDetails.put("email", "test");
    doRegister(userDetails, false);
    assertEquals("Message not found", "An email address must contain a single @", driver.findElement(By.cssSelector(".error")).getText());
    driver.findElement(By.id("email")).clear();
    driver.findElement(By.id("email")).sendKeys("@example.com");
    driver.findElement(By.id("form.submitted")).click();
    assertEquals("Message not found", "The username portion of the email address is invalid (the portion before the @: )", driver.findElement(By.cssSelector(".error")).getText());
    driver.findElement(By.id("email")).clear();
    driver.findElement(By.id("email")).sendKeys("test@example");
    driver.findElement(By.id("form.submitted")).click();
    assertEquals("Message not found", "The domain portion of the email address is invalid (the portion after the @: bob)", driver.findElement(By.cssSelector(".error")).getText());
}
```

Now you have a basic conceptual framework that you can use to start writing tests for your apps. 

All we're doing is using Selenium to input values and make sure that the app's response is exactly what we want. 
Simple as they are, these signup/login/logout tests are extremely valuable. Running them before every deployment 
helps to ensure that you can welcome new users into your community and get them where they need to go.

* _Next_: [Testing local apps with Sauce Connect](##05-Sauce-Connect.md##)