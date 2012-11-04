Testing local apps with Sauce Connect
=======

Developing apps on `localhost` is extremely quick and efficient. However, `localhost` is not a publicly-accessible
address on the Internet, so by default the browsers in the Sauce Labs cloud cannot 
load and test an app that you are running locally.

To get around this limitation, we created [Sauce Connect](https://saucelabs.com/docs/connect).
Sauce Connect uses a secure tunnel protocol that gives specific Sauce machines
access to your local network. Sauce Connect sessions are sandboxed
from outside data flows and are a convenient way to securely test apps that
aren't ready to be deployed on the Internet.

![Sauce Connect](https://raw.github.com/saucelabs/java-tutorial/master/Diagram-Connect.png?login=jlipps&token=bd2ba4272c3899aa616f60ee70c0d128)

To install Sauce Connect, [download](https://saucelabs.com/downloads/Sauce-Connect-latest.zip) the zip file and extract it to your filesystem.

Sauce Connect is a fairly large binary file, so it may take a little while to
download. After it finishes downloading, unzip the zip file and run this command from the directory where the zip file was extracted:

**Mac/Linux/Windows:**

    java -jar Sauce-Connect.jar <your-user-name> <your-access-key>

Since we already configured the Sauce credentials in an earlier tutorial,
Sauce Connect starts up without further ado. It takes a while to load because
it's provisioning a new clean virtual machine to handle the
secure connection. When it says "Connected! You may start your tests..." you
are good to go.

To update your tests to run using Sauce Connect, you will need to change the URL used to create the WebDriver instance to point to localhost:4445 instead of ondemand.saucelabs.com:80, eg.

```java
this.driver = new RemoteWebDriver(
        new URL("http://<your-username>:<your-access-key>@localhost:4445/wd/hub"),
        capabillities);
```

When Sauce Connect is running, all tests that you run using your Sauce Labs
account use the network on the machine where Sauce Connect is located.

For more information about Sauce Connect, or to download and configure the
Java binary on your own, see the [Sauce Connect documentation](https://saucelabs.com/docs/connect).

* _Next_: [Running tests in parallel](https://github.com/saucelabs/java-tutorial/blob/master/06-Parallelism.md)
