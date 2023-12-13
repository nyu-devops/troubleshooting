# Open Shift Issues

## Problem : PostgreSQL permissions error in OpenShift due to incorrect mount path.

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

## Problem : Difficulty logging in using nyu.edu

Difficulty logging into a Red Hat account using university email, with repeated password resets failing to resolve the issue.

### Solution

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

---

## Problem : Error in PyLint and Green Tasks when building Pipeline

Pipeline fails at lint and green tasks due to missing PostgreSQL drivers during `pip install`.

### Solution

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

---

## Problem : Signal 9 , app crashing

App crashes in a loop due to DB connection issues, causing Worker Timeout (Signal 9) in both OpenShift and local cluster.
OR
Getting a 504 gateway time out in BDD or while accessing the URL

### Immediate Solution

Increase memory allocation in `deployment.yaml`:

```yaml
resources:
  limits:
    memory: "256Mi"
  requests:
    memory: "128Mi"
```

<details>
<summary markdown="1">Detailed Explanation</summary>

The issue you're experiencing with your Python Flask application is related to insufficient memory allocation. This is evident from the Worker Timeout (Signal 9) errors in your logs. Signal 9 indicates an Out Of Memory (OOM) error. In a Kubernetes or OpenShift environment, each pod has memory and CPU resources defined in its `deployment.yaml`. When the application exceeds these limits, Kubernetes forcibly terminates the process to maintain the overall health of the system.

Here are the steps to address the issue:

1. **Identify Resource Limits**: Check the `deployment.yaml` for the current resource limits and requests. These values determine the maximum and minimum resources your application can use.
2. **Increase Memory Allocation**: Since the issue is related to memory, you need to increase both the limit and the request for memory. The limit is the maximum amount of memory your application can use, while the request is the amount of memory Kubernetes will guarantee for your application.

   Example modification:

   ```yaml
   resources:
     limits:
       memory: "256Mi"  # Increased from 128Mi
     requests:
       memory: "128Mi"  # Increased from 64Mi
   ```
3. **Apply Changes**: After modifying `deployment.yaml`, apply the changes using `kubectl apply -f deployment.yaml` for a local Kubernetes cluster or the equivalent command in OpenShift.
4. **Monitor the Application**: Once the changes are applied, monitor the application logs and performance to ensure the issue is resolved. If the problem persists, consider profiling the application to understand its memory usage pattern.
5. **Optimize Application**: Long-term, investigate the root cause of high memory usage. It could be due to inefficient database connections, memory leaks, or unoptimized code. Improving the application's memory management can prevent similar issues in the future.

By increasing the memory allocation, you provide more resources for your application to handle database connections and other operations, thereby preventing the Worker Timeout errors.

</details>

---

## Problem : Unable to deploy application in OpenShif

Unable to deploy application in OpenShift using `oc apply -f k8s/deployment.yaml` because the deployment requires an image built by a Continuous Deployment (CD) pipeline.

### Immediate Solution

Deploy the application via a CD pipeline in OpenShift that builds the image with Buildah and then deploys using the generated image.

<details>
<summary markdown="1">Detailed Explanation</summary>

In OpenShift, deploying applications often requires an image built specifically for the OpenShift environment. This is particularly true in more controlled or customized environments where images need to adhere to certain standards or configurations. Simply applying a `deployment.yaml` that references an external image may not suffice.

To resolve this issue:

1. **Set Up a CD Pipeline**: Create a Continuous Deployment pipeline in OpenShift. This pipeline will automate the process of building the application image using Buildah, a tool for building OCI-compatible (Open Container Initiative) container images.
2. **Configure Buildah in the Pipeline**: In your CD pipeline, configure Buildah to build the container image from your application's source code. This process typically involves creating a Dockerfile or using a source-to-image (S2I) builder if you're not using Docker.
3. **Push Image to Registry**: Once the image is built, the pipeline should push it to a container image registry. This could be OpenShift's internal registry or an external one, depending on your setup.
4. **Reference the Built Image in Deployment**: In your `deployment.yaml`, reference the image built by the pipeline. Make sure the image URL and tag match the one produced by the pipeline.
5. **Deploy Using the Pipeline**: Instead of using `oc apply`, deploy your application through the CD pipeline. This ensures that the correct image built for OpenShift is used.
6. **Monitor Deployment**: After deploying through the pipeline, monitor the application to ensure it is running correctly. You can use OpenShift's console or CLI tools to check the status.

This approach ensures that the application is deployed with an image that is built and optimized for the OpenShift environment, leading to more reliable and consistent deployments.

</details>
---

## Problem: 'oc' Command Not Found Despite Installation Attempt

Attempted to install 'oc' using `install-tools.sh` script but encountering 'oc: command not found' error.

### Solution

Copy `install-tools.sh` from `lab-openshift/.devcontainers/script` to your project and rebuild the development container.

<details markdown="1"> 
<summary>Explanation</summary>

1. **Script for Installing Tools**: The `install-tools.sh` script in `lab-openshift/.devcontainers/script` is designed to install necessary tools, including the 'oc' command.
2. **Copying the Script**: Ensure that the `install-tools.sh` script is correctly copied into your project's relevant directory.
3. **Execute the Script**: Run the script within your project. This can usually be done by navigating to the directory containing the script and executing `./install-tools.sh`.
4. **Rebuilding the Development Container**: If you are working within a development container, any changes made to scripts or configurations under `.devcontainers` require you to rebuild the container. This step is crucial for the changes to take effect.
5. **Rebuild Command**: In most development environments, there will be an option to 'Rebuild Container'. This can often be found in the command palette or container settings. Executing this will apply the new configurations and installations.
6. **Verifying Installation**: After rebuilding the container, open a new terminal session within it and try running `oc`. If the script worked correctly, the command should now be recognized.
7. **Troubleshooting Script Execution**: If the script fails to run, ensure it has execute permissions (`chmod +x install-tools.sh`) and your user has the necessary permissions to execute it.

</details>

---

## Problem: Pod Unschedulable Due to Unbound PVCs and Exceeded Resource Limits

Attempting to deploy PostgreSQL using `oc apply k8s/postgres.yaml`, but pod remains in 'pending' state due to unbound PVCs and resource limits.

### Solution

Delete excess PVCs and PipelineRuns, and use the correct `postgresql.yaml` from the lab-openshift repository.

<details markdown="1"> 
<summary>Explanation</summary>

1. **Persistent Volume Claims (PVC) Limit**: Your cluster has a limit of 5 PVCs. Exceeding this limit prevents new pods that require PVCs from being scheduled.
2. **Identifying Necessary PVCs**: You only need two PVCs - `pipeline-pvc` and `postgres-pvc`. Review your PVCs in Admin -> Storage -> PersistentVolumeClaims and delete any others that are not required.
3. **Pod Limits**: There's also a limit of 15 pods in your environment. Exceeding this limit can lead to scheduling issues.
4. **Cleaning PipelineRuns**: Go to the Pipeline section and delete all PipelineRuns. PipelineRuns can create pods that persist and count towards your pod limit.
5. **Using Correct Configuration File**: The `postgresql.yaml` file you're using might not be configured correctly for PostgreSQL. It's essential to use the correct configuration file from the lab-openshift repository, as it has the right settings for mounting PVCs suitable for PostgreSQL.
6. **Impact on Different Environments**: Using the correct `postgresql.yaml` file should not adversely affect your setup in other environments like minikube. The same configuration file should work similarly in both the lab-openshift environment and minikube.
7. **Applying Changes**: After making these adjustments, reapply your `postgres.yaml` or the correct `postgresql.yaml` from the lab-openshift repository using `oc apply -f path_to_file.yaml`.
8. **Verify Pod Status**: Once these changes are made, check the status of your pods to ensure that they are no longer pending and can successfully access the required PVCs.

</details>

---

## Problem: Container in CrashLoopBackOff State in k3d

Container repeatedly crashing in k3d environment, possibly due to issues with the gunicorn command.

### Solution

Update the Dockerfile to use `quay.io/rofrano/python:3.11-slim` image and follow the provided Dockerfile instructions for proper setup.

<details markdown="1"> 
<summary>Explanation</summary>

1. **CrashLoopBackOff Issue**: The `CrashLoopBackOff` status indicates that your container starts but crashes soon after. This can be due to a variety of issues, commonly related to application setup or dependencies.
2. **Inappropriate Base Image**: Using a development base image (`devops-base`) for deployment can cause issues, as it contains tools and settings not suited for a production environment.
3. **Recommended Base Image**: Switch to using `quay.io/rofrano/python:3.11-slim`. This image is lighter and more suitable for production environments.
4. **Dockerfile Adjustments**:
   - **Install Necessary Libraries**: Use `apt-get` to update and install essential libraries like `gcc` and `libpq-dev` for PostgreSQL.
   - **Set Up Working Directory**: Create a working directory `/app` and copy `requirements.txt` into it.
   - **Install Dependencies**: Update pip and wheel, then install dependencies from `requirements.txt` without using cache.
   - **Copy Application Contents**: Copy your service folder into the Docker container.
   - **Non-Root User Setup**: Create a non-root user for security purposes and change ownership of the `/app` directory.
   - **Environment Setup**: Set `FLASK_APP`, `PORT`, and `GUNICORN_BIND` environment variables appropriately.
   - **Entrypoint and Command**: Set `gunicorn` as the entrypoint and specify the command to run your Flask application.
5. **Rebuild and Deploy**: After updating the Dockerfile, rebuild your Docker image and redeploy your container in the k3d environment.
6. **Check Logs**: Use `kc logs <pod_name>` to check logs for any runtime errors that might occur after redeployment.
7. **Verify Deployment**: Ensure that the pod changes from `CrashLoopBackOff` to `Running` state. If issues persist, the logs should provide more specific error information for further troubleshooting.

</details>

---
