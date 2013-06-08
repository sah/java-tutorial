Run this command from your project directory:

    $ mvn test

This launches Maven and will download the dependencies, compiles the source code and run the tests. After a few 
moments you should see that testing has started. You might not see any output instantaneously, but
eventually you will see the following output:

	------------------------------------------------------
	 T E S T S
	-------------------------------------------------------
	Running WebDriverTest
	Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 14.384 sec
	Running WebDriverWithHelperTest
	Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 14.743 sec

	Results :

	Tests run: 2, Failures: 0, Errors: 0, Skipped: 0