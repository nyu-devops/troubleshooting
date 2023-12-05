# Kubernetes Issues

## Kubernetes Issue: Pods Not Reaching READY State

**Problem**: Pods in Kubernetes (k8s) are not reaching the READY state, showing a status of READY 0/1.

**Solution**: Add a liveness probe to your pod configuration using `kubectl`.

<details markdown="1">
<summary>Detailed Explanation</summary>

When a Kubernetes pod shows a status of READY 0/1, it indicates that the pod is running but not ready to receive traffic. This is often due to the readiness probe failing or not being configured.

### What is a Readiness Probe?
A readiness probe is used by Kubernetes to determine if a pod is ready to handle traffic. This is crucial for maintaining service availability and load balancing. 

### Solution Steps:
1. **Identify the Health Endpoint**: Ensure your application has a health check endpoint (e.g., `/health`). This endpoint should return a success status when the application is ready to serve traffic.

2. **Add Readiness Probe to Pod Configuration**:
    - Open your pod configuration YAML file.
    - Add a readiness probe section under the container specification.
    - Specify the probe type (HTTP, TCP, or exec), along with the necessary details like `path`, `port`, and `initialDelaySeconds`.

    Example:
    ```yaml
    readinessProbe:
      httpGet:
        path: /health
        port: 80
      initialDelaySeconds: 10
      periodSeconds: 5
    ```

3. **Apply the Configuration**:
    - Use `kubectl apply -f <your-pod-config.yaml>` to apply the changes.

4. **Monitor Pod Status**:
    - Use `kubectl get pods` to monitor the pod status.
    - The READY status should change to 1/1 once the readiness probe is successful.

### Note:
- The configuration details may vary based on your application's specific needs.
- Ensure that the probe intervals and thresholds are set according to your application's startup time and performance characteristics.

By implementing a readiness probe, Kubernetes can effectively manage traffic to the pods, ensuring that only healthy instances receive requests.

</details>
