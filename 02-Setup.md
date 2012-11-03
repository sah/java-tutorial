Setting up your system to use Sauce with Java
=====

We recommend using [Maven] to build your project and either the [JUnit] or [TestNG]
libraries to write Selenium tests. Maven will download the dependent JAR files
required to run the Selenium tests.


While neither Maven, JUnit or TestNG are required
to use Sauce, this tutorial will assume that this as our framework.

Although this tutorial is not a comprehensive guide for getting Java set up on
your system, here are some guidelines:

Java and Maven Setup for Mac
---

* Continue to [Maven project setup for Mac](#maven_mac)

Java and Maven Setup for Linux
---

* Continue to [Maven project setup for Linux](#maven_linux)

Java and Maven Setup for Windows
---

* Continue to [Maven project setup for Windows](#maven_win)

<a id="maven_mac"></a><a id="maven_linux"></a>Maven Setup for Mac and Linux
---
First, let's create a project directory that we'll use for this tutorial:

    mkdir ~/sauce-tutorial && cd ~/sauce-tutorial

Now we can a Maven command to download and install a sample project using your username and Sauce access key. You can
find your Sauce access key on your [Sauce account page](https://saucelabs.com/account).

To create a JUnit-based project, run:

	mvn archetype:generate \
	-DarchetypeRepository=http://repository-saucelabs.forge.cloudbees.com/release \
	-DarchetypeGroupId=com.saucelabs \
	-DarchetypeArtifactId=quickstart-webdriver-junit \
	-DarchetypeVersion=1.0.7 \
	-DsauceUserName=<your-username> \
	-DsauceAccessKey=<your-access-key>

To create a TestNG-based project, run:
	
	mvn archetype:generate \
	-DarchetypeRepository=http://repository-saucelabs.forge.cloudbees.com/release \
	-DarchetypeGroupId=com.saucelabs \
	-DarchetypeArtifactId=quickstart-webdriver-testng \
	-DarchetypeVersion=1.0.7 \
	-DsauceUserName=<your-username> \
	-DsauceAccessKey=<your-access-key>

You will be prompted to enter a group id (eg. com.yourcompany), artifact id (eg sauce-tutorial), version (defaults to 1.0.0-SNAPSHOT) and package (default to the group id).  Once these values are entered, the sample project files will be created in the ~/sauce-tutorial directory.

Now, you're all set up!

* _Next_: [Running your first test](https://github.com/saucelabs/java-tutorial/blob/master/03-First-Test.md)