# Open Shift Issues

## PostgreSQL permissions error in OpenShift due to incorrect mount path.

### Solution

Set mount path to `/var/lib/postgresql` instead of `/var/lib/postgresql/data`.

<details markdown="1">
<summary>Detailed Explanation</summary>

The error indicates that the PostgreSQL instance in an OpenShift environment lacks the necessary permissions to access the data directory. The solution is to adjust the mount path in the configuration.

Here's a step-by-step guide:

1. Open your PostgreSQL configuration file, typically named `postgresql.yaml` or a similar variation.
2. Locate the `volumeMounts` section.
3. Change the `mountPath` from `/var/lib/postgresql/data` to `/var/lib/postgresql`. This is because the PostgreSQL image expects the data directory to be a subdirectory of the mount path, not the mount path itself.
4. Save the file and apply the configuration to your OpenShift cluster using the appropriate command, such as `oc apply -f postgresql.yaml`.
5. Verify that the PostgreSQL pod starts without the permissions error.

Note: It was also mentioned that changing the image to `postgres:15-alpine` resolved the issue, which suggests that there might be specific configurations or permissions set in the `alpine` image that are more suited to the OpenShift environment.

Remember to adjust the PostgreSQL deployment configurations to ensure that the data persists across pod restarts and deployments.

</details>

## Problem Statement

Difficulty logging into a Red Hat account using university email, with repeated password resets failing to resolve the issue.

### Immediate Solution

Disable any active password manager extensions, and attempt to log in using the email address instead of the user ID.

---

<details markdown="1">
<summary>Detailed Explanation</summary>

#### Context

The user faced challenges accessing their Red Hat account. Despite multiple password resets and receiving confirmation emails, they encountered consistent login failures.

#### Troubleshooting Steps

1. **Password Reset Efforts:** The user reset the password several times and received confirmation emails, indicating that the account and email linkage were active.
2. **Account Lock Possibility:** Repeated failed login attempts might lead to a temporary account lock as a security precaution, but this was not explicitly confirmed in this case.
3. **Impact of Password Managers:** The user eventually identified that a password manager (e.g., LastPass) was affecting the login process. Password managers can autofill outdated credentials or interfere with authentication.

#### Solution

- **Disabling Password Manager:** The user succeeded in logging in after disabling the password manager, using the email address for login rather than the user ID.

#### Conclusion

This scenario underscores the need to consider all aspects of the login procedure, including the potential impact of external tools like password managers, which might complicate the authentication process.

</details>

## Error in PyLint and Green Tasks when building Pipeline

**Problem:**
Pipeline fails at lint and green tasks due to missing PostgreSQL drivers during `pip install`.

**Solution:**
Use `docker.io/rofrano/nyu-devops-base:fa23` for lint and green tasks or install PostgreSQL and gcc before `pip install`.

<details markdown="1">
<summary>Detailed Explanation</summary>

When building a pipeline, if the lint and green tasks fail because they cannot build PostgreSQL drivers during `pip install -r requirements.txt`, there are two primary solutions:

1. **Use a Pre-configured Docker Image:**Replace your current image in the Dockerfile with `docker.io/rofrano/nyu-devops-base:fa23`, which already contains the necessary PostgreSQL libraries. This method avoids the error by providing an environment that's pre-set with the requirements.

   ```yaml
   steps:
     - name: green
       image: rofrano/nyu-devops-base:fa23
       workingDir: $(workspaces.source.path)
   ```
2. **Install Dependencies Manually:**
   Before running `pip install`, ensure that the PostgreSQL library and the gcc compiler are installed. This can be achieved by adding the following steps to your pipeline configuration:

   ```bash
   apt-get update
   apt-get install -y gcc libpq-dev
   pip install -r requirements.txt
   ```

   Note: If using the Docker image as suggested, prefix any `root` commands with `sudo` for appropriate permissions, like `sudo pip install`.

Each method has its benefits: using the pre-configured image simplifies the setup, while manually installing dependencies offers more control over the environment.

</details>
