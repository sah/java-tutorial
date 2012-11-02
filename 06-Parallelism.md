Running tests in parallel
=====

As you may recall from earlier tutorials, Selenium tests can take a long
time! They may take even longer on Sauce because we start each test
on a new virtual machine that has never been used before (don't worry, we don't charge you for the spin-up time).

The solution to this is to run more than one test at a time. Since we have thousands of
clean virtual machines on standby, we encourage you to run as many tests
as you can at once. For an overview of how many tests you can run in parallel, see the parallelization section of our
[plan page](http://saucelabs.com/pricing).

Because PHPUnit doesn't have built-in parallel test execution, we work
around this by using [Paraunit](http://github.com/jlipps/paraunit), a wrapper around PHPUnit that is bundled with Sausage. 
Let's try running some tests in parallel using Paraunit. 

Navigate to the project directory and run the following command. This command specifies the path to the test file 
we want to run and tells Paraunit that we want to simultaneously run two instances of PHPUnit.

**Mac/Linux:**

    vendor/bin/paraunit --processes=2 --path=WebDriverDemo.php

**Windows:**

    vendor\bin\paraunit.bat --processes=2 --path=WebDriverDemo.php

Your tests should run approximately twice as fast as before. You can see the test running simultaneously on your
[Sauce tests page](https://saucelabs.com/tests/). Depending on your Sauce account level, 
consider increasing the number of processes to speed things up even more.

By default tests running in parallel may 
execute in any order. In the next section we'll talk about  
this and discuss some tips for avoiding potential snags when speeding up
test suites using parallelism.

* _Next_: [Tips for better Selenium test performance](https://github.com/saucelabs/php-tutorial/blob/master/07-Tips.md)

