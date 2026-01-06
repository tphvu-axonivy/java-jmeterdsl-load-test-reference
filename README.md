# JMeter Java DSL Performance Test Documentation

## Introduction

This project is providing demo performance tests as a reference for your project using **JMeter Java DSL**, a modern Java-based approach to performance testing that provides a fluent API for creating JMeter test plans programmatically. The JMeter Java DSL eliminates the need for XML-based test plans by allowing you to write tests in pure Java code, making them more maintainable, version-controllable, and easier to integrate into CI/CD pipelines.

### Key Benefits of JMeter Java DSL:
- **Type Safety**: Compile-time validation of test plans
- **IDE Support**: Debugging capabilities
- **Maintainability**: Easy refactoring and code reuse
- **Version Control Friendly**: Pure Java code instead of XML files
- **Integration**: Seamless integration with JUnit and testing frameworks
## Project Structure

```
<your_project_application>/
├── src_test/com/axonivy/
│   └── <your_project_application>Test.java    # Main test class
│
├── resources/
│   ├── test.properties # Configuration properties
│   └── <file_name>.csv # User credentials for testing
│
└── target/
    └── jtls/  # JTL result files
```

## Properties Configuration

The test configuration is managed through `test.properties` file. Here are the key properties you need to configure:

### Server Properties
```properties
# Security System name
# Depends on server setup
# Keep it empty on local environment
security.system.name=

# Application name
application.name=designer

# Project name
project.name=<your_project_name>

# Server host
server.host=localhost

```

### CSV Data Files
```properties
##### CSV file for user
one_user.csv=resources/<file_name>.csv
```

## CSV Data Configuration

### User Credentials Format
CSV files contain user credentials in the format: `username,password`

Example (`users.csv`):
```csv
user1,passwordForUser1
```

### CSV Data Set Configuration in Code
```java
csvDataSet(csvFilePath)
  .variableNames("username,password")  // Define column names
  .delimiter(",")                      // CSV delimiter
  .ignoreFirstLine(false)              // Set to true if CSV has headers
```

## Credential Security Management

### 🔒 Keeping Credentials Secure

Your credential files contain sensitive information and should **never** be committed to version control. Here are several approaches to manage them securely:

> **📝 Note:** This is just the example how files could be used in a project. There're multiple ways to handle Credentials, other solutions are possible.

**Jenkins Secret Files**
1. Go to Jenkins → Manage Jenkins → Manage Credentials
2. Add credentials of type "Secret file" for each CSV file
3. In your Jenkinsfile:
```groovy
pipeline {
  agent any
  stages {
    stage('Setup Credentials') {
      steps {
        script {
          withCredentials([
            // Jenkins > Manage Jenkins > Manage Credentials
            file(credentialsId: 'your_credentials', variable: 'YOUR_CREDENTIALS_CSV'),
          ]) {
            // Copy credential files to expected locations
            sh '''
              cp "$YOUR_CREDENTIALS_CSV" "$<path_to_your_csv_file>"
            '''
          }
        }
      }
    }
    stage('Test') {
      steps {
        bat 'mvn test'
      }
    }
  }
}
```
### Security Best Practices

1. **Never commit actual credentials** to version control
2. **Use template files** to document expected format
3. **Restrict access** to credential files on Jenkins server
4. **Audit credential access** in Jenkins
5. **Rotate credentials** regularly
6. **Use least privilege** principle for test accounts
7. **Monitor test executions** for suspicious activity

## HTTP Samplers Examples

### Basic HTTP Sampler
```java
httpSampler("ProjectStart",
  "/${__P(security.system.name)}/${__P(application.name)}/pro/${__P(project.name)}/1549F58C18A6C562/DefaultApplicationHomePage.ivp")
  .method("GET")
```

### HTTP Sampler with Parameters
```java
httpSampler("Login", "${url}")
  .method("POST")
  .param("javax.faces.partial.ajax", "true")
  .param("javax.faces.source", "login-form:login-command")
  .param("login:login-form:username", "${username}")
  .param("login:login-form:password", "${password}")
  .param("javax.faces.ViewState", "${viewState}")
```

## Passing Values and Variables

### Using properties from test.properties file
Use the `${__P(property.name)}` syntax to reference properties:
```java
.host("${__P(server.host)}") // Gets server.host value from the file
```

### Using Variables from CSV Data
Reference CSV column names as variables:
```java
.param("login:login-form:username", "${username}")  // From csvDataSet variableNames
.param("login:login-form:password", "${password}")  // From csvDataSet variableNames
```

### Using Extracted Variables
Variables extracted by regex extractors can be used in subsequent requests:
```java
httpSampler("Login", "${url}")  // Uses extracted 'url' variable
  .param("javax.faces.ViewState", "${viewState}")  // Uses extracted 'viewState' variable
```

## Regular Expression Extractors

Extractors capture values from HTTP responses for use in subsequent requests:

### Basic Regex Extractor
```java
.children(
  regexExtractor("url", "action=\"([^\"]+)\""),  // Extract form action URL
  regexExtractor("viewState", "id=\"j_id__v_0:javax.faces.ViewState:1\" value=(\"[\\S]+\") ")  // Extract ViewState
)
```
### Redirect URL Extractor
```java
.children(
  regexExtractor("redirectURL", "<redirect url=\"([^\"]+)\">")  // Extract redirect URL from response
)
```

### Extractor Configuration Parameters:
- **First Parameter**: Variable name to store the extracted value
- **Second Parameter**: Regular expression pattern
- **Capture Groups**: Use parentheses `()` to define what to capture
- **Usage**: Access extracted values using `${variableName}`

### Common Regex Patterns:
```java
// Extract URLs from action attributes
regexExtractor("url", "action=\"([^\"]+)\"")

// Extract ViewState from JSF pages
regexExtractor("viewState", "id=\"j_id__v_0:javax.faces.ViewState:1\" value=(\"[\\S]+\") ")

// Extract redirect URLs from XML responses
regexExtractor("redirectURL", "<redirect url=\"([^\"]+)\">")
```

## Response Assertions

Response assertions validate that HTTP requests return expected results:

### Response Code Assertion
```java
.children(
  responseAssertion().fieldToTest(TargetField.RESPONSE_CODE).equalsToStrings("200")
)
```

### Response Body Assertion
```java
.children(
  responseAssertion().fieldToTest(TargetField.RESPONSE_DATA).containsSubstrings("Success")
)
```

### Available Target Fields:
- `TargetField.RESPONSE_CODE`: HTTP status code (200, 404, 500, etc.)
- `TargetField.RESPONSE_DATA`: Response body content
- `TargetField.RESPONSE_HEADERS`: HTTP response headers
- `TargetField.RESPONSE_MESSAGE`: HTTP response message

### Assertion Methods:
- `.equalsToStrings(value)`: Exact match
- `.containsSubstrings(value)`: Contains substring
- `.matchesRegex(pattern)`: Matches regular expression
- `.notContainsSubstrings(value)`: Does not contain substring

## Test Execution Configuration

### Thread Group Configuration
```java
threadGroup("test_name")
  .rampTo(numberOfUsers, Duration.ofSeconds(rampUpPeriod))
  .holdIterating(1)
```

### HTTP Defaults
```java
httpDefaults()
  .host("${__P(server.host)}")  // Default host for all requests
  .port(8081) // Default port for all requests
```

### HTTP Headers
```java
httpHeaders()
  .header("Accept", "text/html,application/xhtml+xml,application/xml;q=0.9")
  .header("Accept-Encoding", "gzip, deflate, br")
  .header("User-Agent", "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36")
```

### Cookies Management
```java
httpCookies()  // Enables automatic cookie handling
```

## Reporting and Results

### JTL Writer (Raw Results)
```java
jtlWriter(jtlDirName, testName + ".jtl")  // Saves raw test results
```

### Results Tree Visualizer (for debugging)
```java
resultsTreeVisualizer()  // Uncomment for local debugging only
```

## Running the Tests

### Execution Commands
```bash
# Run all tests
mvn clean test -Dtest=DemoTest
# or run demo Portal test (you'll need a few manual steps to set up)
# mvn clean test -Dtest=PerformancePortalTest
```

## Quick Setup Guide
1. **Clone the repository**
2. **Run the test**
```bash
mvn clean test -Dtest=DemoTest
# mvn clean test -Dtest=PerformancePortalTest
```
If you want to run `PerformancePortalTest` then you'll need to do these following steps:
- Open the code with Axon Ivy Designer
- Get Portal app from Axon Ivy Market (only Portal is enough). Portal version should be compatible with Designer version
- Update user credential in `one_user.csv` file like Portal app
- Run the test with the commented command from step 2

## Troubleshooting

### Common Issues:
1. **401/403 Errors**: Check user credentials in CSV files
2. **ViewState Errors**: Ensure ViewState extraction regex is correct
3. **Timeout Issues**: Adjust response time expectations in properties
4. **Connection Errors**: Verify server host and port configuration

### Debug Mode:
Uncomment `resultsTreeVisualizer()` for detailed request/response inspection during local development. (see `PerformancePortalTest`)