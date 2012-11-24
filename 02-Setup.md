Setting Up Java, Maven, and JUnit or TestNG for Sauce
=====

We suggest that you consider using [Maven](http://maven.apache.org) to build your Java project and either the 
[JUnit](http://www.junit.org) or the [TestNG](http://www.testng.org) library to write Selenium tests. 

While neither Maven, JUnit nor TestNG are required to write Java tests for Sauce, this is the framework that we'll 
use for this tutorial.

Although this tutorial is not a comprehensive guide for getting Java, Maven, JUnit and TestNG set up on your system, 
here are some guidelines:

<!-- SAUCE:BEGIN_PLATFORM:MAC|LINUX -->
Java and Maven Setup for Mac and Linux
---

Download and install [Java](http://www.java.com/en/download/index.jsp) if it isn't already installed on your system.

Download the [Maven](http://maven.apache.org/download.html) binary distribution and extract it to your file system, 
then add the Maven `bin` directory to your path, for example, 

    export PATH=YOUR_MAVEN_PATH/bin:$PATH

* Continue to [Maven project setup for Mac and Linux](#maven_mac)

<!-- SAUCE:END_PLATFORM -->

<!-- SAUCE:BEGIN_PLATFORM:WIN -->
Java and Maven Setup for Windows
---

Download and install [Java](http://www.java.com/en/download/index.jsp) if it isn't already installed on your system.

Download the [Maven](http://maven.apache.org/download.html) binary distribution and extract it to your file system, 
then add the Maven `bin` directory to your path, for example, 

```bash
set PATH=YOUR_MAVEN_PATH/bin:%PATH%
```

* Continue to [Maven project setup for Windows](#maven_win)

<!-- SAUCE:END_PLATFORM -->

<!-- SAUCE:BEGIN_PLATFORM:MAC|LINUX -->
<a id="maven_mac"></a><a id="maven_linux"></a>JUnit and TestNG Setup for Mac and Linux
---
First, let's create a project directory that we'll use for this tutorial:

```bash
mkdir ~/sauce-tutorial && cd ~/sauce-tutorial
```

Execute the appropriate Maven `mvn` command to download and install a sample JUnit or TestNG project using your Sauce username and your 
Sauce access key. You can find your Sauce access key on your [Sauce account page](https://saucelabs.com/account).

<!-- SAUCE:LOGIN -->

To create a JUnit based project, run:

```bash
mvn archetype:generate \
-DarchetypeRepository=http://repository-saucelabs.forge.cloudbees.com/release \
-DarchetypeGroupId=com.saucelabs \
-DarchetypeArtifactId=quickstart-webdriver-junit \
-DarchetypeVersion=1.0.10 \
-DsauceUserName=<!-- SAUCE:USERNAME --> \
-DsauceAccessKey=<!-- SAUCE:ACCESS_KEY -->
```

To create a TestNG based project, run:
    
```bash
mvn archetype:generate \
-DarchetypeRepository=http://repository-saucelabs.forge.cloudbees.com/release \
-DarchetypeGroupId=com.saucelabs \
-DarchetypeArtifactId=quickstart-webdriver-testng \
-DarchetypeVersion=1.0.10 \
-DsauceUserName=<!-- SAUCE:USERNAME --> \
-DsauceAccessKey=<!-- SAUCE:ACCESS_KEY -->
```

You will be prompted to enter a group id (for example, com.yourcompany), artifact id (for example, 
sauce-tutorial), version (defaults to 1.0.10-SNAPSHOT), and package (defaults to the group id).  
Once these values are entered, the sample project files are created in the `sauce-tutorial` directory.

<!-- SAUCE:END_PLATFORM -->

<!-- SAUCE:BEGIN_PLATFORM:WIN -->
<a id="maven_win"></a>JUnit and TestNG Setup for Windows
---
First, let's create a project directory that we'll use for this tutorial:

    C:\> mkdir C:\sauce-tutorial

Now we execute a Maven command to download and install a sample project using your Sauce username and Sauce access key. 
You can find your Sauce access key on your [Sauce account page](https://saucelabs.com/account).

**JUnit**

To create a JUnit based project, execute the following commands:

    C:\>cd C:\sauce-tutorial

    C:\sauce-tutorial>mvn archetype:generate -DarchetypeRepository=http://repository-saucelabs.forge.cloudbees.com/release     -DarchetypeGroupId=com.saucelabs -DarchetypeArtifactId=quickstart-webdriver-junit -DarchetypeVersion=1.0.10 -DsauceUserName=<!-- SAUCE:USERNAME -->  -DsauceAccessKey=<!-- SAUCE:ACCESS_KEY -->

You will be prompted to enter a group id (for example, com.yourcompany), artifact id (for example, 
sauce-tutorial), version (defaults to 1.0.10-SNAPSHOT) and package (defaults to the group id). 
Once these values are entered, the sample project files are created in the `C:\sauce-tutorial` directory.

**TestNG**

To create a TestNG based project, execute the following commands:
    
    C:\> cd C:\sauce-tutorial

    C:\sauce-tutorial> mvn archetype:generate -DarchetypeRepository=http://repository-saucelabs.forge.cloudbees.com/release     -DarchetypeGroupId=com.saucelabs -DarchetypeArtifactId=quickstart-webdriver-testng -DarchetypeVersion=1.0.10 -DsauceUserName=<!-- SAUCE:USERNAME --> -DsauceAccessKey=<!-- SAUCE:ACCESS_KEY -->

You will be prompted to enter a group id (for example, com.yourcompany), artifact id (for example, 
sauce-tutorial), version (defaults to 1.0.10-SNAPSHOT) and package (defaults to the group id). 
Once these values are entered, the sample project files are created in the `C:\sauce-tutorial` directory.

<!-- SAUCE:END_PLATFORM -->

You're all set up!

* _Next_: [Running your first test](##03-First-Test.md##)