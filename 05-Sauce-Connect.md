Testing Local Apps with Sauce Connect
=======

Developing apps on localhost is extremely quick and efficient. However, localhost is not a publicly-accessible
address on the Internet, so by default the browsers in the Sauce Labs Selenium cloud cannot 
load and test an app that you are running locally.

To get around this limitation, we created [Sauce Connect](https://saucelabs.com/docs/connect). Sauce Connect uses a 
secure tunnel protocol that gives specific Selenium servers in the Sauce cloud access to your local network. 
Sauce Connect sessions are sandboxed from outside data flows and are a convenient way to securely test apps that 
aren't ready to be deployed on the Internet.

![Sauce Connect](##Diagram-Connect.png##)

To install Sauce Connect, [download](https://saucelabs.com/downloads/Sauce-Connect-latest.zip) the Sauce Connect 
zip file and extract it to your filesystem.

Sauce Connect is a fairly large binary file, so it may take a little while to download. After you 
unzip the zip file, and execute the following command from the directory that contains the extracted files (the 
directory that contains the `Sauce-Connect.jar` file):

<!-- SAUCE:LOGIN -->

```bash
java -jar Sauce-Connect.jar <!-- SAUCE:USERNAME --> <!-- SAUCE:ACCESS_KEY -->
```

It takes a while for Sauce Connect to load because it's provisioning a new clean virtual machine in the Sauce Labs cloud 
to handle the secure connection. When it says "Connected! You may start your tests..." you are good to go.

To update your tests to run using Sauce Connect, you will need to change the URL used to create the WebDriver instance 
to point to `localhost:4445` instead of `ondemand.saucelabs.com:80` as shown below:


```java
this.driver = new RemoteWebDriver(
        new URL("http://<!-- SAUCE:USERNAME -->:<!-- SAUCE:ACCESS_KEY -->@localhost:4445/wd/hub"),
        capabilities);
```

Note: When Sauce Connect is running, all tests that you run using your Sauce Labs account use the network on the machine 
on which Sauce Connect is located.

To exit Sauce Connect, enter `Ctrl-C`.

For more information about Sauce Connect, or to download and configure the
Java binary on your own, see the [Sauce Connect documentation](https://saucelabs.com/docs/connect).

* _Next_: [Running tests in parallel](##06-Parallelism.md##)
