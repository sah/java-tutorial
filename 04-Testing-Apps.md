Running tests against web applications
====

Testing a static sandbox is one thing. Testing a real application's functionality
is another. In this tutorial we'll run tests against a real live app sitting
on the web. Specifically, we'll look at Selenium tests for signup and login
behaviors. Once we're done you'll have a good idea how to write Selenium
tests for login and signup in your own app, and you'll have a general understanding
of how to test other site functionality as well.

The test app
---

We have a demo app set up at [http://tutorialapp.saucelabs.com](http://tutorialapp.saucelabs.com) that we can run 
Selenium scripts against. It's a web-based "idea competition" demo app called 
[Shootout](https://github.com/Pylons/shootout). Shootout is a voting platform for ideas designed for the Pyramid Python 
web framework. We won't test voting functionality in this demo, but feel free to play around with it.

The test suite
---

If you downloaded Sausage you'll the have the `WebDriverDemoShootout.php` file in your `sauce-tutorial` directory. It's
reproduced below. However, you can run these 8 tests before we examine them, 
and you can view them in your [Sauce Labs tests page](https://saucelabs.com/tests) just like we did during the first 
test:

**Mac/Linux:**

    vendor/bin/phpunit WebDriverDemoShootout.php

**Windows:**

    vendor\bin\phpunit.bat WebDriverDemoShootout.php

And here's the test suite:

```php
<?php

require_once 'vendor/autoload.php';

class WebDriverDemoShootout extends Sauce\Sausage\WebDriverTestCase
{

    protected $base_url = 'http://tutorialapp.saucelabs.com';

    public static $browsers = array(
        // run FF15 on Windows 8 on Sauce
        array(
            'browserName' => 'firefox',
            'desiredCapabilities' => array(
                'version' => '15',
                'platform' => 'Windows 2012'
            )
    );

    protected function randomUser()
    {
        $id = uniqid();
        return array(
            'username' => "fakeuser_$id",
            'password' => 'testpass',
            'name' => "Fake $id",
            'email' => "$id@fake.com"
        );
    }

    protected function doLogin($username, $password)
    {
        $this->url('/');
        $this->byName('login')->value($username);
        $this->byName('password')->value($password);
        $this->byCss('input.login')->click();

        $this->assertTextPresent("Logged in successfully", $this->byId('message'));
    }

    protected function doLogout()
    {
        $this->url('/logout');
        $this->assertTextPresent("Logged out successfully", $this->byId('message'));
    }

    protected function doRegister($user, $logout = false)
    {
        $user['confirm_password'] = isset($user['confirm_password']) ?
            $user['confirm_password'] : $user['password'];
        $this->url('/register');
        $this->byId('username')->value($user['username']);
        $this->byId('password')->value($user['password']);
        $this->byId('confirm_password')->value($user['confirm_password']);
        $this->byId('name')->value($user['name']);
        $this->byId('email')->value($user['email']);
        $this->byId('form.submitted')->click();

        if ($logout)
            $this->doLogout();
    }

    public function setUp()
    {
        parent::setUp();
        $this->setBrowserUrl('http://tutorialapp.saucelabs.com');
    }

    public function testLoginFailsWithBadCredentials()
    {
        $fake_username = uniqid();
        $fake_password = uniqid();

        $this->byName('login')->value($fake_username);
        $this->byName('password')->value($fake_password);
        $this->byCss('input.login')->click();

        $this->assertTextPresent("Failed to login.", $this->byId('message'));
    }

    public function testLogout()
    {
        $this->doRegister($this->randomUser(), true);
    }

    public function testLogin()
    {
        $user = $this->randomUser();
        $this->doRegister($user, true);
        $this->doLogin($user['username'], $user['password']);
    }

    public function testRegister()
    {
        $user = $this->randomUser();
        $this->doRegister($user);
        $logged_in_text = "You are logged in as {$user['username']}";
        $this->assertTextPresent($logged_in_text);
    }

    public function testRegisterFailsWithoutUsername()
    {
        $user = $this->randomUser();
        $user['username'] = '';
        $this->doRegister($user);
        $this->assertTextPresent("Please enter a value");
    }

    public function testRegisterFailsWithoutName()
    {
        $user = $this->randomUser();
        $user['name'] = '';
        $this->doRegister($user);
        $this->assertTextPresent("Please enter a value");
    }

    public function testRegisterFailsWithMismatchedPasswords()
    {
        $user = $this->randomUser();
        $user['confirm_password'] = uniqid();
        $this->doRegister($user);
        $this->assertTextPresent("Fields do not match");
    }

    public function testRegisterFailsWithBadEmail()
    {
        $user = $this->randomUser();
        $user['email'] = 'test';
        $this->doRegister($user);
        $this->assertTextPresent("An email address must contain a single @");
        $this->byId('email')->clear();
        $this->byId('email')->value('@bob.com');
        $this->byId('form.submitted')->click();
        $this->assertTextPresent("The username portion of the email address is invalid");
        $this->byId('email')->clear();
        $this->byId('email')->value('test@bob');
        $this->byId('form.submitted')->click();
        $this->assertTextPresent("The domain portion of the email address is invalid");
    }

}
```

You're already familiar with a lot of what's going on in this test suite. 
Let's just take a quick look at some new commands and concepts.

Setting `$base_url` allows us to relativize all further uses of `$this->url()`
to a particular domain or URL.

```php
protected $base_url = 'http://tutorialapp.saucelabs.com';
```

The `randomUser()` function generates unique random user details for the registration
and login tests. The randomness is important because it allows our tests to
run in parallel as many times as we want without fear of collisions.

```php
protected function randomUser()
{
    $id = uniqid();
    return array(
        'username' => "fakeuser_$id",
        'password' => 'testpass',
        'name' => "Fake $id",
        'email' => "$id@fake.com"
    );
}
```

The next three functions are helper functions for our tests. If we were testing
a local app, it would be more efficient to perform these actions on the
server-side than to have Selenium generate states of being logged in, logged out,
etc. However, since we're testing an app on the Internet whose backend is inaccessible
to us, we're using these Selenium commands to put the user into these states.

```php
protected function doLogin($username, $password)
{
    $this->url('/');
    $this->byName('login')->value($username);
    $this->byName('password')->value($password);
    $this->byCss('input.login')->click();

    $this->assertTextPresent("Logged in successfully", $this->byId('message'));
}

protected function doLogout()
{
    $this->url('/logout');
    $this->assertTextPresent("Logged out successfully", $this->byId('message'));
}

protected function doRegister($user, $logout = false)
{
    $user['confirm_password'] = isset($user['confirm_password']) ?
        $user['confirm_password'] : $user['password'];
    $this->url('/register');
    $this->byId('username')->value($user['username']);
    $this->byId('password')->value($user['password']);
    $this->byId('confirm_password')->value($user['confirm_password']);
    $this->byId('name')->value($user['name']);
    $this->byId('email')->value($user['email']);
    $this->byId('form.submitted')->click();

    if ($logout)
        $this->doLogout();
}
```

In our first test we make sure that logging in doesn't work with bad username/password
values by asserting that we get a login failure message when we put in random
text.

```php
public function testLoginFailsWithBadCredentials()
{
    $fake_username = uniqid();
    $fake_password = uniqid();

    $this->byName('login')->value($fake_username);
    $this->byName('password')->value($fake_password);
    $this->byCss('input.login')->click();

    $this->assertTextPresent("Failed to login.", $this->byId('message'));
}
```

Next we test login and logout functionality. The first test creates a new user
and uses the `doLogout()` helper function to assert that the logout message
appears. The second test creates a random user, logs it out, and then uses
the `doLogin()` helper function to assert that the successful login message
appears.

```php
public function testLogout()
{
    $this->doRegister($this->randomUser(), true);
}

public function testLogin()
{
    $user = $this->randomUser();
    $this->doRegister($user, true);
    $this->doLogin($user['username'], $user['password']);
}
```

Then we test Shootout's signup functionality by using the
registration helper function to create a new user, then we assert that the
user is logged in (which happens after a successful registration).

```php
public function testRegister()
{
    $user = $this->randomUser();
    $this->doRegister($user);
    $logged_in_text = "You are logged in as {$user['username']}";
    $this->assertTextPresent($logged_in_text);
}
```

And finally we have a set of tests for validation logic in the signup form. First we test that each of the required fields results in an error on signup 
if the field is empty. Next we test if a mismatched password and password
confirmation generate the desired error, and then we test to make sure that the app doesn't allow a successful 
registration if various incorrect email formats are used.

```php
public function testRegisterFailsWithoutUsername()
{
    $user = $this->randomUser();
    $user['username'] = '';
    $this->doRegister($user);
    $this->assertTextPresent("Please enter a value");
}

public function testRegisterFailsWithoutName()
{
    $user = $this->randomUser();
    $user['name'] = '';
    $this->doRegister($user);
    $this->assertTextPresent("Please enter a value");
}

public function testRegisterFailsWithMismatchedPasswords()
{
    $user = $this->randomUser();
    $user['confirm_password'] = uniqid();
    $this->doRegister($user);
    $this->assertTextPresent("Fields do not match");
}

public function testRegisterFailsWithBadEmail()
{
    $user = $this->randomUser();
    $user['email'] = 'test';
    $this->doRegister($user);
    $this->assertTextPresent("An email address must contain a single @");
    $this->byId('email')->clear();
    $this->byId('email')->value('@bob.com');
    $this->byId('form.submitted')->click();
    $this->assertTextPresent("The username portion of the email address is invalid");
    $this->byId('email')->clear();
    $this->byId('email')->value('test@bob');
    $this->byId('form.submitted')->click();
    $this->assertTextPresent("The domain portion of the email address is invalid");
}
```

Now you have a basic
conceptual framework that you can use to start writing tests for your apps. 

All we're doing is 
using Selenium to input values and make sure that the app's response is
exactly what we want. Simple as they are, these login/signup tests are extremely valuable. 
Running them before every deployment will help ensure that you can 
welcome new users into your community and get them where they need to go.


* _Next_: [Testing local apps with Sauce Connect](https://github.com/saucelabs/java-tutorial/blob/master/05-Sauce-Connect.md)

