# boilerPlate_Serenity_java
# Boilerplate for Serenity BDD/Cucumber Screenplay

## Getting Started with Serenity and Cucumber

Serenity BDD is a powerful library that simplifies the creation of high-quality automated acceptance tests while offering robust reporting and living documentation features. It provides strong support for both web testing using Selenium and API testing with RestAssured.

Serenity promotes excellent test automation design and accommodates various design patterns, including classic Page Objects, the more modern Lean Page Objects/Action Classes approach, and the sophisticated and flexible Screenplay pattern.

The latest version of Serenity supports Cucumber 6.x.

## Project Directory Structure

This project includes build scripts for Maven and adheres to the standard directory structure commonly used in most Serenity projects:


Feel free to explore the project's Maven build scripts and the directories mentioned above to get started with your Serenity BDD/Cucumber Screenplay project.


src
+ main
    + java
+ test
    + java                        Test runners and supporting code
    + resources
        + features                  Feature files 
             + Login.feature
        + web
             + testdata
        + serenity.conf
    + target
    + pom.xml
    + 
          Serenity 2.2.13 introduced integration with WebdriverManager to download webdriver binaries.

In the `main/java` directory, you will find various packages and classes essential for the execution of our tests. Within this boilerplate project, the `dataReader` package plays a pivotal role as it contains all the classes responsible for reading JSON data.

However, the cornerstone of our project lies in the `pages` package, where we embrace the Page Object Model (POM) design pattern. Each page within this package comprises classes dedicated to its locators, the page itself, questions (interrogations about the page), and interactions with it. The primary motivation behind implementing the POM is to promote code reusability and maintainability.

Additionally, within the project, you will discover classes dedicated to handling JSON data and WebDriver, both of which are invaluable for facilitating our test execution.


In the `test` directory, you will find a `java` folder housing various classes relevant to the test suite, including step definitions, hooks, acceptance tests, and more.

Meanwhile, the `resources` directory within `test` contains:

- `features`: This directory is home to your Cucumber feature files (with the `.feature` extension).
- `testData`: You can store your test data files here.
- `webdriver`: Here, you'll find WebDriver configurations.
- `serenity.conf`: This file holds your Serenity configuration settings.

These directories collectively provide the structure and organization needed for developing and running Serenity BDD/Cucumber Screenplay tests effectively.


The sample scenario
Both variations of the sample project uses the sample Cucumber scenario. In this scenario, the user fill the contact us form

Feature: user fill the contact us form

Scenario: user fill the contact us form
Given user is on Emumba home page
When user clicks on the Contact us button
And user fills the contact us form
Then user should see the success message

Screenplay classes emphasise reusable components and a very readable declarative style, whereas Lean Page Objects and Action Classes (that you can see further down) opt for a more imperative style.

The NavigateTo class is responsible for opening the Emumba home page:

public class NavigateTo {
public static Performable homePage() {
return Task.where("{0} opens the Emumba home page",
Open.browserOn().the(homePage.class));
}
}
It does this using a standard Serenity Page Object. Page Objects are often very minimal, storing just the URL of the page itself:

@DefaultUrl("https://www.emumba.com/")
public class homePage extends PageObject {}

Executing the tests
To run the sample project, you can either just run the CucumberTestSuite test runner class, or run either mvn verify or gradle test from the command line.

By default, the tests will run using Chrome. You can run them in Firefox by overriding the driver system property, e.g.

$ mvn clean verify -Ddriver=firefox
Or

$ gradle clean test -Pdriver=firefox
The test results will be recorded in the target/site/serenity directory.

Generating the reports
Since the Serenity reports contain aggregate information about all of the tests, they are not generated after each individual test (as this would be extremenly inefficient). Rather, The Full Serenity reports are generated by the serenity-maven-plugin. You can trigger this by running mvn serenity:aggregate from the command line or from your IDE.

They reports are also integrated into the Maven build process: the following code in the pom.xml file causes the reports to be generated automatically once all the tests have completed when you run mvn verify?

             <plugin>
                <groupId>net.serenity-bdd.maven.plugins</groupId>
                <artifactId>serenity-maven-plugin</artifactId>
                <version>${serenity.maven.version}</version>
                <configuration>
                    <tags>${tags}</tags>
                </configuration>
                <executions>
                    <execution>
                        <id>serenity-reports</id>
                        <phase>post-integration-test</phase>
                        <goals>
                            <goal>aggregate</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
Simplified WebDriver configuration and other Serenity extras
The sample projects both use some Serenity features which make configuring the tests easier. In particular, Serenity uses the serenity.conf file in the src/test/resources directory to configure test execution options.

Webdriver configuration
The WebDriver configuration is managed entirely from this file, as illustrated below:

webdriver {
driver = chrome
}
headless.mode = true

chrome.switches="""--start-maximized;--test-type;--no-sandbox;--ignore-certificate-errors;
--disable-popup-blocking;--disable-default-apps;--disable-extensions-file-access-check;
--incognito;--disable-infobars,--disable-gpu"""
Serenity uses WebDriverManager to download the WebDriver binaries automatically before the tests are executed.

Environment-specific configurations
We can also configure environment-specific properties and options, so that the tests can be run in different environments. Here, we configure three environments, dev, staging and prod, with different starting URLs for each:

environments {
default {
webdriver.base.url = "https://myapp.myorg.com"
}
dev {
webdriver.base.url = "https://myapp.myorg.com/dev"
}
staging {
webdriver.base.url = "https://myapp.myorg.com/staging"
}
prod {
webdriver.base.url = "https://myapp.myorg.com/prod"
}
}
You use the environment system property to determine which environment to run against. For example to run the tests in the staging environment, you could run:

$ mvn clean verify -Denvironment=staging
See this article for more details about this feature.


