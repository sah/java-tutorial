Setting up your system to use Sauce with Java
=====

We recommend using [Maven](http://maven.apache.org) to build your project and either the [JUnit](http://www.junit.org) or [TestNG](http://www.testng.org) libraries to write Selenium tests. 

While neither Maven, JUnit or TestNG are required
to use Sauce, this tutorial will assume that this is our framework.

Although this tutorial is not a comprehensive guide for getting Java and Maven set up on
your system, here are some guidelines:

Java and Maven Setup for Mac
---

Download and install [Java](http://www.java.com/en/download/index.jsp) if it isn't already installed on your system.

Go to the [Maven download](http://maven.apache.org/download.html) page to download the Maven binary distribution and extract it to your file system.  Add the `bin` directory to your path, eg.

	export PATH=YOUR_MAVEN_PATH/bin:$PATH

* Continue to [Maven project setup for Mac](#maven_mac)

Java and Maven Setup for Linux
---

Download and install [Java](http://www.java.com/en/download/index.jsp) if it isn't already installed on your system.

Go to the [Maven download](http://maven.apache.org/download.html) page to download the Maven binary distribution and extract it to your file system.  Add the `bin` directory to your path, eg.

	export PATH=YOUR_MAVEN_PATH/bin:$PATH

* Continue to [Maven project setup for Linux](#maven_linux)

Java and Maven Setup for Windows
---

Download and install [Java](http://www.java.com/en/download/index.jsp) if it isn't already installed on your system.

Go to the [Maven download](http://maven.apache.org/download.html) page to download the Maven binary distribution and extract it to your file system.  Add the `bin` directory to your path, eg.

	set PATH=YOUR_MAVEN_PATH/bin:%PATH%

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
	-DarchetypeVersion=1.0.10 \
	-DsauceUserName=<your-username> \
	-DsauceAccessKey=<your-access-key>

To create a TestNG-based project, run:
	
	mvn archetype:generate \
	-DarchetypeRepository=http://repository-saucelabs.forge.cloudbees.com/release \
	-DarchetypeGroupId=com.saucelabs \
	-DarchetypeArtifactId=quickstart-webdriver-testng \
	-DarchetypeVersion=1.0.10 \
	-DsauceUserName=<your-username> \
	-DsauceAccessKey=<your-access-key>

You will be prompted to enter a group id (eg. com.yourcompany), artifact id (eg sauce-tutorial), version (defaults to 1.0.0-SNAPSHOT) and package (default to the group id).  Once these values are entered, the sample project files will be created in the ~sauce-tutorial directory.

<a id="maven_win"></a>Maven Setup for Windows
---
First, let's create a project directory that we'll use for this tutorial:

    C:\> mkdir C:\sauce-tutorial

Now we can a Maven command to download and install a sample project using your username and Sauce access key. You can
find your Sauce access key on your [Sauce account page](https://saucelabs.com/account).

To create a JUnit-based project, run:

	C:\> cd C:\sauce-tutorial

	C:\sauce-tutorial> mvn archetype:generate -DarchetypeRepository=http://repository-saucelabs.forge.cloudbees.com/release 	-DarchetypeGroupId=com.saucelabs -DarchetypeArtifactId=quickstart-webdriver-junit -DarchetypeVersion=1.0.7 -DsauceUserName=<your-username> 	-DsauceAccessKey=<your-access-key>

To create a TestNG-based project, run:
	
	C:\> cd C:\sauce-tutorial

	C:\sauce-tutorial> mvn archetype:generate -DarchetypeRepository=http://repository-saucelabs.forge.cloudbees.com/release 	-DarchetypeGroupId=com.saucelabs -DarchetypeArtifactId=quickstart-webdriver-testng -DarchetypeVersion=1.0.7	-DsauceUserName=<your-username>	-DsauceAccessKey=<your-access-key>

You will be prompted to enter a group id (eg. com.yourcompany), artifact id (eg sauce-tutorial), version (defaults to 1.0.0-SNAPSHOT) and package (default to the group id).  Once these values are entered, the sample project files will be created in the C:\sauce-tutorial directory.

Now, you're all set up!

* _Next_: [Running your first test](https://github.com/saucelabs/java-tutorial/blob/master/03-First-Test.md)