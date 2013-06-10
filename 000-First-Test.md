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

    StringBuilder url = new StringBuilder();
    url.append("http://")
	.append(auth.getUsername())
	.append(":")
	.append(auth.getAccessKey())
	.append("@ondemand.saucelabs.com:80/wd/hub");

    this.driver = new RemoteWebDriver(new URL(url.toString()), this.caps);
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

Next we define our test set up. First we start by creating a SauceOnDemandAuthentication object, it will manage our 
credentials for us. Secondly we create a TestName object, this will let our test have nice meaningfull names. 
```java
public SauceOnDemandAuthentication auth = new SauceOnDemandAuthentication();
@Rule public TestName testName = new TestName();
```

Now to the actual setup. The following sets the selenium test name to the test methods name. And creates a WebDriver object
with your credentials that will be used to run your tests in the sauce cloud.
```java
@Before
public void setUp() throws Exception {
    this.caps.setCapability("name", this.testName.getMethodName());

    StringBuilder url = new StringBuilder();
    url.append("http://")
	.append(auth.getUsername())
	.append(":")
	.append(auth.getAccessKey())
	.append("@ondemand.saucelabs.com:80/wd/hub");

    this.driver = new RemoteWebDriver(new URL(url.toString()), this.caps);
}
```

Tests!
___
So let's write our first test.
```java
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
```

Run your first test suite
---
Now that Maven is installed and your archetype is created, let's run our tests:

<!-- SAUCE:INCLUDE:run_maven -->

While the tests are running head to your [Sauce Labs account
page](https://saucelabs.com/account) and you can see them running.
You can even watch them running live if you want by clicking the test names!

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

