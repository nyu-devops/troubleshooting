# Behaviour Driven Development Issues

## Problem : Selenium WebDriver Not Found

---

**Problem:** Selenium tests in Docker fail with error: "WebDriverException: Message: 'chromedriver' executable needs to be in PATH."

**Solution:** Use the `rofrano/pipeline-selenium` Docker image, which includes Chrome and chromedriver, in `.devcontainer/Dockerfile`.

<details markdown="1">
<summary>Detailed Explanation:</summary>

This issue occurs when the Selenium WebDriver, specifically 'chromedriver', is not found in the Docker container's PATH. To resolve this:

1.**Switch Docker Image**: Update the Dockerfile to use `rofrano/pipeline-selenium`. This image is pre-configured with Chrome and chromedriver.
3. **Rebuild Docker Container**: After updating the Dockerfile, **rebuild** the container to ensure the new configuration is applied.
4. **Verify Installation**: Check if 'chromedriver' is correctly installed and accessible by running a test command inside the container.

</details>

---

## Problem: Behave Command Not Found

---

**Problem:** When running behave in a Docker container, an error indicates that the command is not found.

**Solution:** Ensure `behave` is listed in the `requirements.txt` file and the Docker container is rebuilt. Add the below

```
# Behavior Driven Development
behave==1.2.6
selenium==4.10.0
compare==0.2b0
requests==2.31.0

```

<details markdown="1">
<summary>Detailed Explanation:</summary>

The error indicating that the 'behave' command is not found suggests it is not installed in the Docker container. To fix this:

1.**Check requirements.txt**: Ensure `behave` is listed in the `requirements.txt` file.
2. **Rebuild Container**: Rebuild the Docker container to install `behave` from the updated `requirements.txt`.
3. **Test Behave Installation**: Run a simple behave command to confirm it's now recognized in the container.

## Problem : Chromedriver Unstable or Failing

---

**Problem:** Chromedriver is inconsistent or fails in Docker environments when running Selenium tests.

**Solution:** Initially, test with `export DRIVER=firefox`. If this resolves the issue, make it permanent by setting `DRIVER: firefox` and `WAIT_SECONDS: 3` in `.devcontainer/docker-compose.yml`.

<details markdown="1">
<summary>Detailed Explanation:</summary>

To address Chromedriver's inconsistent behavior across different systems, follow these steps:

1.**Temporary WebDriver Switch**:
   - Run `export DRIVER=firefox` in the Docker environment to temporarily switch to Firefox WebDriver.
   - This change applies only to the current session and helps determine if Firefox WebDriver resolves the issue.

2.**Test the Change**:
   - Conduct your Selenium tests again to check if the issue with Chromedriver is resolved using Firefox WebDriver.

3.**Permanent Configuration**:
   - If the issue is resolved with Firefox, then make this change permanent.
   - In your project's `.devcontainer/docker-compose.yml`, add the following under the `environment` section:
   ``        environment:            DRIVER: firefox            WAIT_SECONDS: 3        ``
   - `DRIVER: firefox` sets Firefox as the default WebDriver.
   - `WAIT_SECONDS: 3` reduces the wait time in case of errors, speeding up test execution.

4.**Rebuild Docker Environment**:
   - After updating the `docker-compose.yml` file, rebuild the Docker environment to apply these changes.
   - This ensures that all future tests will automatically use Firefox WebDriver.

5.**Verify Stability**:
   - Run the tests again in the updated Docker environment to confirm the stability and consistency of Selenium tests with Firefox WebDriver.

</details>

---

### Problem Statement

Behavior-driven development tests with `behave` are failing due to an assertion error despite receiving a 200 HTTP status code.

### Immediate Solution

Ensure the database is correctly set up by running `flask db-create` and then execute `honcho start` to run the application with the correct environment.

---

<details markdown="1">
<summary>Detailed Explanation</summary>

#### Context

A user is encountering assertion errors when running `behave` tests, even though the application seems to be running correctly as indicated by the 200 HTTP status code received from a service.

#### Possible Causes

1. **Database Issues:** As mentioned by the user, the error was related to the database (DB) table not being set up correctly.
2. **Environment Setup:** The suggestion to run `honcho start` implies that the application's environment variables might not have been initialized properly.

#### Steps to Resolve

1. **Database Initialization:**

   - Run `flask db-create` to set up the database tables correctly.
   - Ensure that the database is properly migrated and seeded with any required initial data.
2. **Application Environment:**

   - Use `honcho start` to run the application. Honcho reads from a `.env` file to set environment variables, ensuring that the application runs in the correct context.

#### Conclusion

The user's problem was resolved by setting up the database tables correctly and ensuring that the application's environment was correctly initialized using Honcho. This highlights the importance of having both the database and environment correctly configured for behavior-driven development tests to pass.

</details>
