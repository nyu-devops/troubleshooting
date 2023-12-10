# GitHub Actions Issues

## Problem Statement: make lint works locally, but not in the GA workflow

Users are experiencing issues where **`make lint`** runs successfully in a local environment but fails during the linting step in the GitHub Actions workflow.

### Solution:

To resolve the discrepancies between local linting and GitHub Actions, users should update their workflow configuration to match the **`workflow.yml`** file provided in the "[Lab Flask TDD Workflow](https://github.com/nyu-devops/lab-flask-tdd/blob/master/.github/workflows/ci.yml)." Additionally, they have two options for addressing the linting step.

<details markdown="1">
<summary>Detailed Explanation:</summary>

**Understanding the Problem:**

- Differences between local and CI/CD environments can cause inconsistencies in test results.
- The GitHub Actions environment must be configured to mirror the local setup as closely as possible.
- The **`make lint`** command must be standardized across environments to ensure that it uses the same parameters and dependencies.

**Step-by-Step Solution:**

1. **Update GitHub Actions Workflow:**
   - Ensure that the **`.github/workflows/workflow.yml`** file is consistent with the one provided in the "Lab Flask TDD Workflow."
   - The workflow file should define the same environment and steps as used locally.
2. **Option 1 - Update `workflow.yml`:**
   - Locate line 67 in the **`workflow.yml`** file and replace it with:

     ```yaml
     yamlCopy code
     green -vvv --processes=1 --run-coverage --termcolor --minimum-coverage=95

     ```
   - This ensures that the GitHub Actions workflow uses the same linting command as the local environment.
3. **Option 2 - Update `requirements.txt`:**
   - Include **`pytest`** and related packages in the **`requirements.txt`** file to match the local testing dependencies:

     ```python

     # Testing dependencies
     pytest==7.4.0
     pytest-pspec==0.0.4
     pytest-cov==4.1.0

     ```
   - By doing so, you ensure that all necessary testing libraries are available in the GitHub Actions environment.
4. **Modify the Makefile:**
   - If opting for the second solution, update line 37 in the **`Makefile`** to use **`pytest`** instead of the existing command:

     ```bash
     pytest --pspec --cov=service --cov-fail-under=95
     ```
   - This change aligns the local linting command with the one used in GitHub Actions, ensuring consistency.
5. **Re-run GitHub Actions:**
   - Commit and push the changes to trigger the GitHub Actions workflow.
   - Monitor the Actions tab in the GitHub repository to ensure that the linting step passes successfully.

</details>
