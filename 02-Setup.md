Setting Up Java and Maven for Sauce
=====

We suggest that you consider using [Maven](http://maven.apache.org) to build your Java project and either the 
[JUnit](http://www.junit.org) or the [TestNG](http://www.testng.org) library to write Selenium tests. While neither 
Maven, JUnit nor TestNG are required to write Java tests for Sauce, this is the framework that we'll use for this 
tutorial.

Although this tutorial is not a comprehensive guide for setting up Java, Maven, JUnit and TestNG on your system, 
here are some guidelines:

<!-- SAUCE:BEGIN_PLATFORM:MAC|LINUX -->
Java and Maven Setup for Mac and Linux
---

[Download](http://www.java.com/en/download/index.jsp) and install Java if it isn't already installed on your system.

[Download](http://maven.apache.org/download.html) and install the Maven binary distribution and extract it to your 
file system, then add the Maven `bin` directory to your path, for example, 

    export PATH=YOUR_MAVEN_PATH/bin:$PATH

* Continue to [Maven project setup for Mac and Linux](#maven_mac)

<!-- SAUCE:END_PLATFORM -->

<!-- SAUCE:BEGIN_PLATFORM:WIN -->
Java and Maven Setup for Windows
---

[Download](http://www.java.com/en/download/index.jsp) and install Java if it isn't already installed on your system.

[Download](http://maven.apache.org/download.html) and install the Maven binary distribution and follow the installation 
instructions to extract it to your file system. 

Next, follow the Maven installation instructions to add:
* The appropriate user environment variables and PATH for Maven.
* The user environment variable and system Path for Java.

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

You will be prompted to enter a groupId (for example, com.yourcompany), artifactId (this is the directory that
the files will be created in, for example 
sauce-tutorial), version (defaults to 1.0-SNAPSHOT), and package (defaults to the groupId).  
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

    C:\sauce-tutorial>mvn archetype:generate -DarchetypeRepository=http://repository-saucelabs.forge.cloudbees.com/release -DarchetypeGroupId=com.saucelabs -DarchetypeArtifactId=quickstart-webdriver-junit -DarchetypeVersion=1.0.10 -DsauceUserName=<!-- SAUCE:USERNAME --> -DsauceAccessKey=<!-- SAUCE:ACCESS_KEY -->

You will be prompted to enter a groupId (for example, com.yourcompany), artifactId (this is the directory that
the files will be created in, for example 
sauce-tutorial), version (defaults to 1.0.10-SNAPSHOT) and package (defaults to the groupId). 
Once these values are entered, the sample project files are created in the `C:\sauce-tutorial` directory.

**TestNG**

To create a TestNG based project, execute the following commands:
    
    C:\>cd C:\sauce-tutorial

    C:\sauce-tutorial>mvn archetype:generate -DarchetypeRepository=http://repository-saucelabs.forge.cloudbees.com/release -DarchetypeGroupId=com.saucelabs -DarchetypeArtifactId=quickstart-webdriver-testng -DarchetypeVersion=1.0.10 -DsauceUserName=<!-- SAUCE:USERNAME --> -DsauceAccessKey=<!-- SAUCE:ACCESS_KEY -->

You will be prompted to enter a groupId (for example, com.yourcompany), artifactId (this is the directory that
the files will be created in, for example 
sauce-tutorial), version (defaults to 1.0-SNAPSHOT) and package (defaults to the groupId). 
Once these values are entered, the sample project files are created in the `C:\sauce-tutorial` directory.

<!-- SAUCE:END_PLATFORM -->

You're all set up!

* _Next_: [Running your first test](##03-First-Test.md##)