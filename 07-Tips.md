Tips for better Selenium test performance
=====

In this section we'll share some tips about how to improve the performance of Selenium tests.

Avoid test dependencies
---

Simply put, dependencies between tests prevent your tests from being able to run in parallel. And running your tests in parallel is by far
the best way to speed up the execution of your entire test suite. It's much
easier to add a virtual machine than to try to figure out how to squeeze out another second
of performance from a single test.

What are dependencies? Imagine if we had a test suite with these two tests:

```php
function testLogin()
{
    // do some stuff to trigger a login
    $this->assertEquals("My Logged In Page", $this->title());
}

function testUserOnlyFunctionality()
{
    $this->byId('userOnlyButton')->click();
    $this->assertTextPresent("Result of clicking userOnlyButton");
}
```

In the first function's pseudocode the `testLogin()` function triggers the
browser to log in 
and asserts that the login was successful. The second test clicks a button on the
logged-in page and asserts that a certain result occurred.

This test suite works fine as long as the tests run in order. But the
assumption the second test makes (that we are already logged in) creates a 
dependency on the first test. If these tests run at the same time, or if the
second one runs before the first one, the browser's cookies will
not yet allow Selenium to access the logged-in page and the second test fails.

The right way to get rid of these dependencies is to make sure each test can 
run completely independently of the other. Let's fix the example above so
there are no dependencies:


```php
function doLogin()
{
    // do some stuff to trigger a login
    $this->assertEquals("My Logged In Page", $this->title());
}

function testLogin()
{
    $this->doLogin();
}

function testUserOnlyFunctionality()
{
    $this->doLogin();
    $this->byId('userOnlyButton')->click();
    $this->assertTextPresent("Result of clicking userOnlyButton");
}
```

The main point is that it is dangerous to assume any state whatsoever when
developing tests for your app. Instead, find ways to quickly generate
desired states for individual tests the way we did above with the `doLogin()` function 
-- it generates a logged-in state instead of assuming it. You might
even want to develop an API for the development and test versions of your app
that provides URL shortcuts that generate common states. For example, 
a URL that's only available in test that creates a random user account and 
logs it in automatically.

Don't use brittle locators
---
WebDriver provides a number of [locator strategies](http://code.google.com/p/selenium/wiki/JsonWireProtocol#/session/:sessionId/element) for accessing elements on a webpage. 
It's tempting to use complex XPath expressions like `//body/div/div/*[@class="someClass"]`
or CSS selectors like `#content .wrapper .main`. While these might work when
you are developing your tests, they will almost certainly break when you make
unrelated refactoring changes to your HTML output.

Instead, use sensible semantics for CSS IDs and form element names, and try to
restrict yourself to using `$this->byId()` or `$this->byName()`. This makes it 
much less likely that you'll inadvertently break your page by shuffling some
lines of code around.

Use spinAsserts
----
One issue with Selenium is that it doesn't know when it's supposed to wait
before executing the next command. When people click a submit button,
they know not to check for text or try another action until the next page
loads. Selenium, however, immediately executes the next command. If
the next command is part of an assertion in your code the assertion may
fail, even though if Selenium had waited a little bit longer it would have succeeded.

The solution is to use a special kind of assertion called a spinAssert that was introduced earlier in this tutorial. 
This kind of assertion only fails after retrying periodically over a specified amount of time.

The definition of the spinAssert function in Sausage looks like this:

```php
public function spinAssert($msg, $test, $args=array(), $timeout=10)
```

* `$msg` is the message that's passed to the Exception if the spinAssert fails.
* `$test` is the function we are going to use for the assertion. It can be:
  * An [anonymous function](http://php.net/manual/en/functions.anonymous.php)
  * A string representing a function name
  * An array of the form ```array($class_name, $method_name)```
* `$args` is an array of arguments that's passed to the test function.
* `$timeout` is an integer representing the number of seconds to spin.

The spinAssert function calls the `$test` function once every second until
the `$timeout` seconds limit is reached. If the `$test` function returns a true 
value the assertion succeeds If the `$test` function returns a false value the test is 
retried every second until it hits `$timeout` seconds. If it hits `$timeout` seconds the
assertion fails.

If spinAssert example earlier in the tutorial didn't have a spinAssert it would have looked like this:

```php
public function testSubmitComments()
{
    $comment = "This is a very insightful comment.";
    $this->byId('comments')->click();
    $this->keys($comment);
    $this->byId('submit')->submit();
    $this->assertEquals($this->byId('your_comments')->text(), "Your comments: $comment");
}
```

The trouble with this is that after Selenium hits the submit button it 
immediately gets the text from the comment results box (the element 
id `#your_comments`). If the page didn't have time to get updated from the
server the assertion fails even though it would have succeeded if Selenium had waited a 
bit longer. 

Let's look at the same function using spinAssert:

```php
public function testSubmitComments()
{
    $comment = "This is a very insightful comment.";
    $this->byId('comments')->click();
    $this->keys($comment);
    $this->byId('submit')->submit();
    $driver = $this;

    $comment_test =
        function() use ($comment, $driver)
        {
            return ($driver->byId('your_comments')->text() == "Your comments: $comment");
        }
    ;

    $this->spinAssert("Comment never showed up!", $comment_test);

}
```

In this function, we create an anonymous closure called `$comment_test` that
returns true if the comments field has the right text in it and false
if it doesn't. Then we call `$this->spinAssert` and pass it the test function.
We're using the default timeout of 10 seconds, so our script keeps retrying 
the Selenium `text()` command until its value is what we expect or we hit 10 seconds.

Using spinAssert functions is a great way to make tests less brittle and more
accepting of differences in network speeds, surges in traffic, and other challenges in the test environment.

* _Finally_: [Next steps and more information](https://github.com/saucelabs/java-tutorial/blob/master/08-Info.md)
