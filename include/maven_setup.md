<!-- SAUCE:BEGIN_PLATFORM:MAC|LINUX -->
<a id="maven_mac"></a><a id="maven_linux"></a>Java and Maven Setup for Mac and Linux
---

Download and install [Java](http://www.java.com/en/download/index.jsp) if it isn't already installed on your system.

Go to the [Maven download](http://maven.apache.org/download.html) page to download the Maven binary distribution and extract it to your file system.  Add the `bin` directory to your path, for example, 

```bash
	export PATH=YOUR_MAVEN_PATH/bin:$PATH
```

First, let's create a project directory that we'll use for this tutorial:

```bash
	mkdir ~/sauce-java-tutorial && cd ~/sauce-java-tutorial
```

Now we execute a Maven command to download and install a sample project using your Sauce username and your Sauce access key. You can
find your Sauce access key on your [Sauce account page](https://saucelabs.com/account).

<!-- SAUCE:LOGIN -->

To create a JUnit based project, run:

```bash
	mvn archetype:generate \
	-DarchetypeRepository=http://repository-saucelabs.forge.cloudbees.com/release \
	-DarchetypeGroupId=com.saucelabs \
	-DarchetypeArtifactId=quickstart-webdriver-junit \
	-DarchetypeVersion=1.0.11 \
	-DsauceUserName=<!-- SAUCE:USERNAME --> \
	-DsauceAccessKey=<!-- SAUCE:ACCESS_KEY -->
```

To create a TestNG based project, run:

```bash
	mvn archetype:generate \
	-DarchetypeRepository=http://repository-saucelabs.forge.cloudbees.com/release \
	-DarchetypeGroupId=com.saucelabs \
	-DarchetypeArtifactId=quickstart-webdriver-testng \
	-DarchetypeVersion=1.0.11 \
	-DsauceUserName=<!-- SAUCE:USERNAME --> \
	-DsauceAccessKey=<!-- SAUCE:ACCESS_KEY -->
```

You will be prompted to enter a group id (for example, com.yourcompany), artifact id (for example, sauce-java-tutorial), version (defaults to 1.0.0-SNAPSHOT), and package (default to the group id).  Once these values are entered, the sample project files will be created in the ~sauce-tutorial directory.

<!-- SAUCE:END_PLATFORM -->

<!-- SAUCE:BEGIN_PLATFORM:WIN -->
<a id="maven_win"></a>Java and Maven Setup for Windows
---

Download and install [Java](http://www.java.com/en/download/index.jsp) if it isn't already installed on your system.

Go to the [Maven download](http://maven.apache.org/download.html) page to download the Maven binary distribution and extract it to your file system.  Add the `bin` directory to your path, for example, 

```bash
set PATH=YOUR_MAVEN_PATH/bin:%PATH%
```

First, let's create a project directory that we'll use for this tutorial:

    C:\> mkdir C:\sauce-java-tutorial

Now we execute a Maven command to download and install a sample project using your Sauce username and Sauce access key. You can
find your Sauce access key on your [Sauce account page](https://saucelabs.com/account).

**JUnit**

To create a JUnit based project, execute the following commands:

	C:\> cd C:\sauce-java-tutorial

	C:\sauce-java-tutorial>mvn archetype:generate -DarchetypeRepository=http://repository-saucelabs.forge.cloudbees.com/release 	-DarchetypeGroupId=com.saucelabs -DarchetypeArtifactId=quickstart-webdriver-junit -DarchetypeVersion=1.0.11 -DsauceUserName=<!-- SAUCE:USERNAME --> 	-DsauceAccessKey=<!-- SAUCE:ACCESS_KEY -->

You will be prompted to enter a group id (for example, com.yourcompany), artifact id (for example, sauce-java-tutorial), version (defaults to 1.0.0-SNAPSHOT) and package (default to the group id).  Once these values are entered, the sample project files will be created in the `C:\sauce-java-tutorial` directory.

**TestNG**

To create a TestNG based project, execute the following commands:
	
	C:\> cd C:\sauce-tutorial

	C:\sauce-tutorial> mvn archetype:generate -DarchetypeRepository=http://repository-saucelabs.forge.cloudbees.com/release 	-DarchetypeGroupId=com.saucelabs -DarchetypeArtifactId=quickstart-webdriver-testng -DarchetypeVersion=1.0.11 -DsauceUserName=<!-- SAUCE:USERNAME -->	-DsauceAccessKey=<!-- SAUCE:ACCESS_KEY -->

You will be prompted to enter a group id (for example, com.yourcompany), artifact id (for example, sauce-java-tutorial), version (defaults to 1.0.0-SNAPSHOT) and package (default to the group id).  Once these values are entered, the sample project files will be created in the `C:\sauce-java-tutorial` directory.

<!-- SAUCE:END_PLATFORM -->
