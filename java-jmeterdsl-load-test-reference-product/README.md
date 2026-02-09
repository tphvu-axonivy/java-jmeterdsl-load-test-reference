# JMeter Java DSL Performance Test

This project provides demo performance tests as a reference for your Axon Ivy project using **JMeter Java DSL**, a modern Java-based approach to performance testing. It offers a fluent API for creating JMeter test plans programmatically, eliminating the need for XML-based test plans by allowing you to write tests in pure Java code.

**Key Benefits:**
- **Type Safety**: Compile-time validation of test plans
- **IDE Support**: Full debugging capabilities
- **Maintainability**: Easy refactoring and code reuse
- **Version Control Friendly**: Pure Java code instead of XML files
- **Integration**: Seamless integration with JUnit and testing frameworks

## Demo

### DemoTest

A simple smoke test that verifies external website availability:
- Sends HTTP GET requests to `axonivy.com` and `market.axonivy.com`
- Validates HTTP 200 response codes and expected page content
- Generates JTL result files and HTML reports

### PerformancePortalTest

A full Portal walkthrough test simulating a real user session:
1. **Login** — Authenticates with credentials from a CSV file
2. **Portal Home** — Navigates to the home page
3. **Processes** — Opens the process list
4. **Task List** — Navigates to the task list dashboard
5. **Case List** — Navigates to the case list dashboard
6. **Logout** — Ends the session

This test validates response codes at every step, extracts dynamic values (ViewState, redirect URLs) via regex, and reports results as JTL and HTML.

### PerformancePortalTestReviewInGui

Same Portal walkthrough as above, but opens the **JMeter GUI** (`resultsTreeVisualizer`) for visual debugging and request/response inspection during local development.

## Setup

### Project Structure
```
<your_project_application>/
├── src_test/com/axonivy/
│   └── <your_project_application>Test.java
├── resources/
│   ├── test.properties
│   └── <file_name>.csv
└── target/
    └── jtls/
```

### Quick Start
1. Clone the repository
2. Run the simple demo test:
```bash
mvn clean test -Dtest=DemoTest
```
3. To run `PerformancePortalTest`:
   - Open the code with Axon Ivy Designer
   - Install Portal from Axon Ivy Market (version must be compatible with Designer)
   - Update user credentials in `one_user.csv`
   - Run: `mvn clean test -Dtest=PerformancePortalTest`

### Configuration

Configure your test via `resources/test.properties`:

```
@variables.yaml@
```
