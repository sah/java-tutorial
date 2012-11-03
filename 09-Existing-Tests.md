Integrating Sauce into existing PHPUnit WebDriver Tests
=======

Do you already have a test suite running WebDriver tests? While we recommend
using
[Sausage](https://github.com/saucelabs/java-tutorial/blob/master/02-Setup.md),
it's also possible to integrate with Sauce in whichever test framework you
happen to be using -- you'll just miss out on a lot of features you get for
free in Sausage (like automatic pass/fail reporting).  Let's take a look at an
example `PHPUnit_Extensions_Selenium2TestCase` test suite, so we can see what
needs to be modified to have it running on Sauce.

Before Sauce Integration
----
Below is a very simple example test suite consisting of two tests. Right now,
this test suite directly subclasses `PHPUnit_Extensions_Selenium2TestCase`,
and thereby uses a locally-running Firefox browser:

```php
<?php

require_once 'vendor/autoload.php';

class WebDriverExample extends \PHPUnit_Extensions_Selenium2TestCase
{
    public static $browsers = array(
        array(
            'browserName' => 'firefox',
            )
        )
    );

    public function setUp()
    {
        parent::setUp();
        $this->setBrowserUrl('http:/saucelabs.com/test/guinea-pig');
    }

    public function testTitle()
    {
        $this->assertContains("I am a page title", $this->title());
    }

    public function testLink()
    {
        $link = $this->byId('i am a link');
        $link->click();
        $this->assertContains("I am another page title", $this->title());
    }
}
```

After Sauce Integration
----
Integrating is simple: we simply add a set of desired capabilities, and point
to a different Selenium server, one in our cloud of browsers. To ensure only
you have access to the tests you run, we'll need to augment the server address
with your Sauce username and access key (found on your [account page](http://saucelabs.com/account)).

Note that in this example, we assume these values are stored in PHP constants
(`SAUCE_USERNAME` and `SAUCE_ACCESS_KEY` respectively), defined wherever you like.

```php
<?php

require_once 'vendor/autoload.php';

define('SAUCE_HOST', SAUCE_USERNAME.':'.SAUCE_ACCESS_KEY.'@ondemand.saucelabs.com');

class WebDriverExample extends \PHPUnit_Extensions_Selenium2TestCase
{
    public static $browsers = array(
        array(
            'browserName' => 'firefox',
            'host' => SAUCE_HOST,
            'port' => 80,
            'desiredCapabilities' => array(
                'version' => '15',
                'platform' => 'Windows 2012'
            )
        )
    );

    public function setUp()
    {
        parent::setUp();
        $this->setBrowserUrl('http:/saucelabs.com/test/guinea-pig');
    }

    public function testTitle()
    {
        $this->assertContains("I am a page title", $this->title());
    }

    public function testLink()
    {
        $link = $this->byId('i am a link');
        $link->click();
        $this->assertContains("I am another page title", $this->title());
    }
}
```

So you can see, getting going on Sauce with your existing tests is really quite
simple. However, by migrating to Sausage you get several useful features in
addition to a more convenient interface. Pass/fail reporting, spinAsserts,
and more streamlined browser configuration, etc...

If you're interested in seeing how to install and configure Sausage, check out
the [Setup
tutorial](https://github.com/saucelabs/java-tutorial/blob/master/02-Setup.md).
Meanwhile, happy testing!
