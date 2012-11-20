How Selenium WebDriver and Sauce Labs Work
=============

[Selenium](http://seleniumhq.org) is a technology that allows programmers to
send commands to web browsers to make them perform tasks as
though they were being used by a human. In this sense it's like a
robot for web browsing.

Selenium allows test engineers to write scripts that exercise 
web site functionality. These scripts can be run as many times as we want, which
makes testing a web application something our code can do, rather than forcing
someone to click around on a web page to make sure that it works.

Selenium comes in two parts:

1. A server that listens for commands from your test script.
2. A set of browser extensions (called Drivers) that enable a browser to be
remotely controlled by the server.

The Selenium server is actually an HTTP server, which makes talking to it easy
and available to any programming language with a good HTTP library (like Java).
There are a number of Java libraries designed to talk to the Selenium server,
so luckily we don't have to think at all about making HTTP commands.

There is one wrinkle, however, due to the history of Selenium's development.
There are currently two versions of Selenium in use by developers: Selenium 1
(also known as Selenium RC) and Selenium 2 (also known as Selenium WebDriver).
In this tutorial we'll be talking exclusively about WebDriver, so make sure
if you do any research about these topics that you know which version of
Selenium is being discussed.

Selenium can be visualized like this:

![Selenium architecture](##Diagram-Selenium.png##)

In this diagram, all of the components (the Java test class, the Selenium server,
and the browsers with the Selenium extensions installed) run on your computer
(or on a computer in your locally-accessible network). Your code (written
in one of the Selenium libraries for Java) sends commands to the Selenium
server, which relays them to the browsers you have available and active.
The results are relayed back from the browsers, through the Selenium server, and
to your code so you can determine whether a given test was successful or not.

While all of these tools are free and open source, it can sometimes be quite
a pain to set up and manage a suite of Selenium-ready browsers. If you want
full cross-browser compatibility, you'll have to wander into the land of 
virtualization in order to install browsers on multiple operating systems.
Likewise, if you want to run many tests at a time, you'll soon run into
hardware limitations and maintenance nightmares.

Sauce Labs is designed to be a drop-in replacement for all of that
infrastructure. We install and maintain Selenium servers and browsers so you
don't have to: your script stays exactly the same, but instead of talking
to a Selenium server that you set up on your own computer, your script talks to a Selenium server running
in the Sauce Labs cloud. Check out this diagram:

![Sauce architecture](##Diagram-Sauce.png##)

The only difference when using Sauce Labs to run your Selenium scripts is that
the Internet is the communication layer between your Java code and the
Selenium server. In addition, you have access to a much broader range of browsers than
you would have on your local machine, including mobile devices like the iPhone,
iPad, and various Android devices.

Enough about us! Let's get your system set up to run Selenium scripts on the Sauce Labs cloud.

* _Next_: [Setting up your system to use Sauce with Java](##02-Setup.md##)


