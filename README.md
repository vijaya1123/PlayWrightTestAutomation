Welcome to the Playwright TypeScript Framework. This unique and comprehensive automation framework is designed to simplify and streamline the process of writing and managing automated tests for Web (Desktop & Mobile), APIs, and Electron Desktop applications. It's built on Playwright, a powerful browser automation library, and TypeScript, a statically typed superset of JavaScript, offering a robust and efficient environment for end-to-end testing.

This framework is ideal for QA professionals, developers, and business analysts looking to improve their testing practices and efficiency. It's equipped with utilities that simplify test creation and maintenance, allowing you to focus on writing your tests out of the box.

Key Features:
Unique Page Object Model Design Pattern: Our Page Object Model (POM) design stands apart from traditional POMs. It's a unique approach that significantly reduces complexity and accelerates coding, making it easier and faster to write scripts compared to traditional POMs. This means less time spent on setup and more time spent on creating effective tests. Discover how our approach differs with the traditional POM.

Ease of Use: Designed to be intuitive and user-friendly, making it an excellent choice for beginners to understand and write scripts. This means less time spent on learning the tool and more time spent on creating effective tests.

User-Friendly for All Roles: This framework is not just for QA Automation professionals. Developers, Manual QA, and Business Analysts can also contribute to end-to-end testing, promoting collaboration across different departments and roles.

Utility Functions: Simplifies common actions and assertions, such as clicking buttons, filling forms, and checking elements. It also includes inbuilt methods for conditional statements and maintains a default LoadState across applications.

Customizable: Easily adaptable to meet individual project needs, fitting seamlessly into any project, regardless of its specific requirements or constraints.

Inbuilt CI/CD Support with GitHub Actions: Facilitates continuous integration and continuous delivery with inbuilt support for GitHub actions, automating your testing process from code integration to delivery.

Versatile Support: It facilitates testing across Web (Desktop & Mobile),APIs and, Electron Desktop apps, allowing comprehensive testing across different platforms and applications.

Detailed Reporting: Provides screenshots, videos, and traces of test failures, making it easier to understand and fix issues.

Local Web Server: Allows you to test UI code changes directly on your local machine or on a VM by effortlessly initializing a local web server, eliminating the need for a separate testing environment.

In summary, the Playwright TypeScript Framework is a powerful, flexible, and user-friendly tool that leverages the power of Playwright and TypeScript. It's an excellent choice for teams looking to improve their testing practices and efficiency.

Table of Contents
Getting Started
Tools & Frameworks
Prerequisites
Installation
Project Update Guide
Project Structure
Framework Setup
Page set up
Usage
Working with Page Objects
Creating a Spec File
Utilities
Page Utilities
Locator Utilities
Handling Frames
Action Utilities
Managing Alerts
Element Utilities
Assertion Utilities
Optional Parameter Type Objects
Test annotations
Executing Tests
Using the Playwright Plugin
Command-Line Execution
Report Generation and Viewing
Additional Playwright Features
Best Practices
Contribution guide
Getting Started
Tools & Frameworks
TypeScript: A statically typed superset of JavaScript programming language, enhancing code quality and understandability.
Playwright Test: A modern end-to-end testing framework, facilitating test creation, execution, fixture management, and report generation.
Playwright Assertions: Provides robust assertion capabilities for validating test outcomes.
Allure Report: A flexible and visually appealing reporting tool, offering clarity on test results.
ESLint: A pluggable linting utility for JavaScript and TypeScript, ensuring code consistency and detecting potential errors.
Prettier: An opinionated code formatter, ensuring consistent code style across the project.
Logger (Winston): A versatile logging library, producing both file-based logs and color-coded console outputs.
Husky: Manages Git hooks to enforce quality checks, such as linting, before commits.
Github Actions: A CI/CD platform integrated with GitHub, automating workflows like test execution on pull requests. Enhanced with Playwright's CI guide.
Prerequisites
Before you begin, there are some essential requirements you must meet. Please refer to the Prerequisites section for detailed information on the necessary software and tools needed to run this project.

Installation
Get started with the project by following the step-by-step installation guide. Please refer to the Installation section for complete instructions on setting up the project on your local machine.

Project Update Guide
Keeping your project up to date is crucial. Please refer to the Project Update Guide section for guidelines on how to update your project to the latest version.

Project Structure
Understanding the project's architecture is key to working with the code. Please refer to the Project Structure section for an overview of the directory layout and file organization.

Framework Setup
Learn how to configure and customize the framework to suit your needs. Please refer to the Framework Setup section for detailed instructions.

Page set up and Switching Pages
Managing page navigation is a common task in web testing. Please refer to the Pages section for techniques and examples on how to set up a page and switch between different pages within your tests.

Usage
Page Objects
Page objects are utilized to encapsulate information about the elements present on each page of your application. They also provide a structured way to write action and assertion functions for various functionalities on each page. This approach promotes code reusability and makes the tests easier to maintain and understand. Page objects can be found in the pages directory.

Here's an example of a page object under the pages package:

sauce-demo-login-page.ts

//importing utility functions
import { click, clickAndNavigate, fill, gotoURL } from '@ActionUtils';
import { failureLoginCredentials, successLoginCredentials } from '../../testdata/sauce-demo-test-data';
import { expectElementToBeVisible } from '@AssertUtils';
import { getLocator, getLocatorByPlaceholder, getLocatorByRole } from '@LocatorUtils';

const userName = `#user-name`;
const password = () => getLocator(`#password`).or(getLocatorByPlaceholder('Password', { exact: true }));
const login = () => getLocatorByRole('button', { name: 'Login' });
const errorMessage = `//*[contains(@class,'error-message')]`;

export async function navigateToSauceDemoLoginPage() {
  await gotoURL('https://www.saucedemo.com/');
}

export async function logInSuccessfully() {
  await fill(userName, successLoginCredentials.username);
  await fill(password(), successLoginCredentials.password);
  await clickAndNavigate(login());
}

export async function failureLogin() {
  await fill(userName, failureLoginCredentials.username);
  await fill(password(), failureLoginCredentials.password);
  await click(login());
}

export async function verifyErrorMessageForFailureLogin() {
  await expectElementToBeVisible(errorMessage);
}

export async function verifyLoginPageisDisplayed() {
  await expectElementToBeVisible(userName);
}
In this example, the sauce-demo-login-page represents the login page within the application. It includes methods to navigate to the Saucedemo homepage, execute both successful and unsuccessful login actions, verify the success of the login in the successful login scenario, and confirm the display of an error message in the case of a failed login.

Refer to the Utilities section on how to use the reusable methods.

Refer to the Running Tests section below on how to run tests.

Writing Tests in a spec file
Tests are written in the specs directory. Each test file should correspond to a specific feature or functionality of the application under test. Tests are constructed using Page objects.

Here's an example of a test file under the specs directory:

sauce-demo-preferred-pom.spec.ts

//import test from PageSetup.ts which sets up the page before each test
import { test } from '@PageSetup';

//importing page objects to use all functions within that page to construct the tests
import * as LoginPage from 'tests/pages/preferredPOM/sauce-demo-login-page';
import * as MiniCart from 'tests/pages/preferredPOM/sauce-demo-mini-cart';
import * as ProductsPage from 'tests/pages/preferredPOM/sauce-demo-products-page';

test.describe('Saucedemo tests for successful, unsuccessful logins and add product to cart', () => {
  test('Saucedemo tests - Successful login will display Products Page', async () => {
    await LoginPage.navigateToSauceDemoLoginPage();
    await LoginPage.logInSuccessfully();
    //verifying products page is displayed on successful login
    await ProductsPage.verifyProductsPageDisplayed();
  });

  test('Saucedemo test - Add product to cart', async () => {
    await LoginPage.navigateToSauceDemoLoginPage();
    await LoginPage.logInSuccessfully();
    await ProductsPage.verifyProductsPageDisplayed();
    await ProductsPage.addToCartByProductNumber(1);
    //verifying mini cart count is updated to 1
    await MiniCart.verifyMiniCartCount('1');
  });

  test('Saucedemo test - When login is unsuccessful will not display Products Page', async () => {
    await LoginPage.navigateToSauceDemoLoginPage();
    await LoginPage.failureLogin();
    await LoginPage.verifyErrorMessageForFailureLogin();
    //verifying Login is still displayed
    await LoginPage.verifyLoginPageisDisplayed();
    //verifying Products Page is not displayed
    await ProductsPage.verifyProductsPageNotDisplayed();
  });
});
In this example, we are setting the page state by importing test from @PageSetup and writing the spec file. Here are some important points to note:

Import test from @PageSetup instead from @playwright/test. page-setup is customized for this framework to set the page state. This ensures that the page is set up correctly before each test.

setPage function from page-setup file will set the page state before each test and is imported to our spec files while executing the tests. If you want to use the Playwright page directly to write our tests, we can use getPage function from page-utils file. The page object is managed by the framework, and we can use the setPage and getPage functions to set and get the page state, ensuring that all of the pages operate on the same page object.

We first navigate to the home page, then perform the login action, and finally verify if the login was successful.

In this example, the LoginPage represents a login page within the application. It includes methods to navigate to the homepage, perform a login action, and check if the login was successful. Similarly, ProductsPage and MiniCart are also page objects that have functions for their respective pages.

Utilities
Explore various utility functions and helpers that can make your testing more efficient. The Utilities section in this project encompasses a variety of functions designed to enhance the efficiency of your testing process. These utilities include:

Page Utilities: Functions that assist in setting and getting the page object.
Locator Utilities: Functions that assist in locating elements on the page, making it easier to interact with them.
Action Utilities: Functions that encapsulate common actions like clicking, typing, or dragging, providing a more concise way to express these operations in your tests.
Element Utilities: Functions for handling conditional statements with web elements, such as checking if an element is visible, hidden, or contains certain text or input values.
Assertion Utilities: Helpers that simplify the process of making assertions about the state of the application, enhancing the readability and maintainability of your tests.
Optional Parameter Type Objects: Provides a set of options for utility modules.
Please refer to the Utilities section for a comprehensive guide to the available utilities in this project, including detailed descriptions and examples of how to use them.

Executing Tests
We have the flexibility to execute a single test, a specific set of tests, or the entire test suite. Testing can be carried out on a single browser or across multiple browsers simultaneously. By default, tests run in a headless mode, and the test outcomes are displayed in the terminal.

Run tests using plugin
Playwright Test for VSCode plugin empowers you to run specific tests or entire test suites directly from the editor. You can conveniently trigger tests with a click, making it efficient to validate changes.

For detailed guidance on plugin installation, configuring test settings in the playwright.config file, and executing tests, please visit Executing tests using a Playwright plugin.

Parallel Execution
Playwright allows you to execute tests in parallel across multiple workers. This can significantly speed up your test suite.

To enable parallel execution, add the following line at the top of your spec file, above the test block:

test.describe.configure({ mode: 'parallel' });
The number of workers can be configured either in the playwright.config file or via the command-line interface.

Running Tests via the Command-Line Interface
Utilize a variety of commands to execute your tests in different modes. Below are a few illustrative examples:

npm run commands
The package.json file contains several scripts designed to streamline test execution. Here are a few common examples:

To run a single test in chromium headed mode (i.e., with the browser UI visible), use the grep command to specify the test:
npm run test:chromium-headed -- -g 'login test'
To run all the tests in a spec file in chromium headless mode:
npm run test:chromium -- nucleus.spec.ts
To run all the tests in multiple spec files in headed mode:
npm run test:chromium-headed -- checkout.spec.ts nucleus.spec.ts
To run all the tests in a spec file with 3 threads, and 2 retries in chromium headless mode:
npm run test:chromium -- nucleus.spec.ts -j 3 --retries 2
To run in debug mode:
npm run test:chromium-headed -- nucleus.spec.ts --debug
To run all the smoke tests using the tag in headless mode for all the projects:
npm run test -- -g '@smoke'
To run all tests in headless mode for all the projects:
npm run test
npx playwright test commands
You can use the following playwright commands as well to run your tests:

npx playwright test -c playwright.config.ts -g "logo is present @reg" --headed -j 1 --retries 0
Here's what each option does:

-c: Specifies the configuration file for Playwright. In this case, it's playwright.config.ts.
-g: This is the "grep" option, which allows you to run specific tests instead of all of them. In this case, it's running the test with the name "logo is present @reg".
-j: Specifies the number of workers to use. In this case, it's 1.
--retries: Specifies the number of times to retry failed tests. In this case, it's 0.
--headed: Runs the tests in headed mode. By default, tests are run in headless mode.
--project=chromium: Runs the tests in the Playwright Chromium browser. By default, it will run all the projects in the playwright.config.ts
--repeat-each 3: Repeats each test 3 times. This is useful for verifying the stability of a test or checking intermittent issue fixes locally by running the test multiple times.
--grep-invert: The opposite of -g or grep. It filters out the tests to run.
--max-failures 4: Stops after the first 4 test failures. This includes a count of failures in the retry test as well.
--list: Lists all the tests, but does not run them.
CLI Parallel Execution

For more information, please refer to the Playwright CLI documentation.

Report Generation and Viewing
Playwright Test offers several built-in reporters tailored for various requirements, along with the flexibility to integrate custom reporters. You can configure these reporters either through the command line or within the playwright.config.ts file. For a comprehensive guide on Playwright's in-built reporters, refer to the official documentation.

Accessing Reports via Command-Line Interface (CLI)
Playwright command: After executing tests, you can view the reports using the following command:
npx playwright show-report <path to the report>
Framework Configured script: This framework's configuration for viewing reports is defined in the package.json under the scripts section:
"report": "playwright show-report playwright-report"
To access the reports post-test execution using this configuration, run:

npm run report
Additional Playwright Features
UI Mode: Playwright's UI mode allows you to explore, run, and debug tests in a watch mode. Dive deeper into this feature here.

Test Generator: With Playwright, you can automatically generate tests. It inspects the page to determine the optimal locator, prioritizing by role, text, and test ID locators. Learn more about test generation using Codegen here.

Trace Viewer: The Playwright Trace Viewer offers a graphical interface to review recorded traces post-execution. Get more details here.

Best Practices
Here are some recommended best practices when using this framework:

Use Utility Functions: Whenever possible, use the Utilities functions provided in the framework instead of directly using Playwright methods. These utility functions are designed to simplify common tasks and make your tests more readable and maintainable.

Feedback on Utility Functions: If you find that a utility function for a specific action or assertion is missing, please provide feedback so we can continue to improve and expand our utility Functions. Meanwhile, temporarily use the corresponding Playwright method combined with getPage from @PageSetup for a specific task, the utility function is not available. Replace these with newly added utility functions once they are available.

Conditional Statements: Instead of manually implementing waits, use functions like isElementVisible, isElementChecked from elementutils. These functions automatically wait for the element to become visible, with customizable timeout options. It's advisable to avoid using these for assertions; instead, utilize AssertUtils wherever possible.

Retrieving Text and Input Values: To fetch texts or input values, consider functions like getAllTexts and getAllInputValues from elementutils. These methods come with built-in waits, ensuring they only proceed once an element is available, preventing premature returns of an empty Array.

clickandNavigate vs click: If a click action triggers page navigation, use the clickandNavigate utility function instead of the click function. clickandNavigate function includes built-in checks for frame navigation and waits for a new page to load. Use the click function if it is an Ajax call when you don't navigate to a different page.

Fill vs Type: Use the fill utility function as default to fill form fields. Use type when you want to simulate typing character by character, such as when testing auto-search suggestions or autofill features. Playwright type documentation

Web-First Assertions: Prioritize using playwright web-first assertions in your tests instead of jest or other library assertions. Playwright Web First Assertions documentation

Soft Assertions for Non-Critical Checks: Use a soft assertion when a test assertion isn't critical, allowing the test to continue and fail at the end. For critical assertions, use a hard assertion i.e. the default assertion. Playwright Soft Assertions documentation

Backticks for Xpath & CSS: Always use backticks for Xpath & CSS selectors so that you can use single & double quotes together if needed without any escape characters.

Playwright's Inbuilt Features: Playwright comes with a wide range of inbuilt features. Make sure to utilize these as needed.

Local Webserver
Mocking
Parallelism and sharding
Playwright Configuration
use Options
TestConfig
Mobile Emulation
Playwright Test methods
Debugging
In addition to these, Playwright also recommends following certain best practices. You can find more details in the Playwright Best Practices documentation
