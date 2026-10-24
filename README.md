# Selenium + Apache Ant + JUnit 5

Java project using Selenium 4.50.0 and JUnit Jupiter 5.11.4 (JUnit Platform 1.11.4). Ant builds and launches tests; Apache Ivy downloads transitive dependency JARs. No Maven, TestNG, or database is required.

## Windows prerequisites
1. Install a full JDK 21 (Java sources target Java 17 or newer). Set JAVA_HOME to the JDK directory and add its bin directory to PATH.
2. Download the Apache Ant binary ZIP from https://ant.apache.org/bindownload.cgi and extract it. Set ANT_HOME to its directory and add %ANT_HOME%/bin to PATH. Restart your terminal.
3. Install Google Chrome. Selenium Manager obtains ChromeDriver automatically; first use needs internet access.
4. Verify `java -version`, `javac -version`, and `ant -version`.

## Run from the project directory
```bat
ant check
ant smoke
ant test
ant -Dheadless=true test
ant -Dtest.pattern=.*LoginTest test
ant clean
```
- `check`: compile all sources and run three configuration tests without a browser.
- `smoke`: test a bundled local HTML form using Chrome. It does not need the practice website, but driver setup may need internet.
- `test`: run all tests including three login scenarios on https://the-internet.herokuapp.com.
- `clean`: remove build output only. Downloaded JARs remain cached.

Always put -D overrides before the target. Edit config.properties for the URL and timeouts. Edit build.properties for headless mode or dependency versions. Dependency downloads require HTTPS access to repo.maven.apache.org. Ivy's user cache is also used. No downloaded JARs or browsers are bundled in this archive.

## Eclipse
1. Extract the ZIP. File > Import > General > Existing Projects into Workspace; select SeleniumAntJUnit.
2. Right-click build.xml > Run As > Ant Build; choose `resolve`. Use a full JDK in the Ant launch JRE tab.
3. Refresh the project. Project > Properties > Java Build Path > Libraries > Classpath > Add JARs. Select every JAR under this project's lib folder. Apply and Close. Do not add a second JUnit library: the standalone JAR already includes JUnit.
4. Confirm the project uses JDK 21 (Java Build Path > JRE System Library). Clean the project if needed.
5. Right-click ConfigTest.java, LocalSmokeTest.java, or LoginTest.java > Run As > JUnit Test. Select JUnit 5 if asked. Alternatively run the `test` Ant target.
6. For headless Eclipse JUnit execution add -Dheadless=true under Run Configurations > JUnit > Arguments > VM arguments.

## Layout
- build.xml: Ant targets; JUnit ConsoleLauncher runs in a forked JVM with a failing exit code on test failure.
- ivy.xml / ivysettings.xml: dependency resolution from Maven Central, without requiring Maven.
- BaseTest: JUnit @BeforeEach/@AfterEach Chrome setup and cleanup.
- LoginPage: locators, actions, and explicit waits.
- LoginTest: parameterized valid login, invalid username, invalid password; screenshots on failure.
- LocalSmokeTest: browser setup and form verification without an external test site.
- ConfigTest: configuration checks without a browser.
- build/reports: JUnit XML reports after execution.
- build/screenshots: login failure screenshots.

Tests run sequentially by default, with a fresh browser per browser-test invocation. No sleeps or implicit waits are used. The sample credentials are the public practice site's demonstration credentials. Never add real credentials to source control.

## Troubleshooting
- `javac` missing: use a full JDK, not only a JRE.
- Ivy download errors: check proxy/firewall access to Maven Central; configure Ant/Ivy proxy settings for your network. Do not disable TLS validation.
- Driver error: remove stale webdriver.chrome.driver settings; permit Selenium Manager downloads.
- A local smoke pass with login failures points to the practice website, networking, or changed page locators.
- Ant failure status is intentional when assertions fail. Check the console and build/reports XML files.
- This is a separate JUnit project: existing TestNG BaseTest and annotations cannot be copied over unchanged.
