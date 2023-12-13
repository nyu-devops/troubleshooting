# General Issues

## Problem : Service fails to start due to port 8000 / 8080 already in use.

Service fails to start due to port 8000 / 8080 already in use.

### Solution

Kill process on port 8000: `sudo fuser -k 8000/tcp`
Kill process on port 8000: `sudo fuser -k 8080/tcp`

<details markdown="1">
<summary>Detailed Explanation</summary>

When a service doesn't start and complains about a port being in use, it indicates another process is already listening on that port. Port conflicts are common when running multiple services on the same machine, especially during development.

The command `sudo fuser -k 8000/tcp` is used to kill the process that is using TCP port 8000. Here's what each part does:

- `sudo`: Run the command with superuser privileges, necessary for affecting other processes.
- `fuser`: A utility to show which processes use the specified files, sockets, or filesystems.
- `-k`: Kill processes accessing the file (in this case, the port).
- `8000/tcp`: Specifies the TCP port 8000 to be checked and acted upon.

After freeing up the port, you can start your service. If using `honcho`, which is a Python tool that manages Procfile-based applications, run `honcho start` to start the services defined in your `Procfile`.

**Note:** Be cautious when killing processes, as you might terminate something important. Always check what is running on that port if possible.

</details>



## Problem : `ModuleNotFoundError: No module named 'psycopg`

Encountering `ModuleNotFoundError: No module named 'psycopg'` error in a Python application.

### Immediate Solution

Update the PostgreSQL driver in `requirements.txt` and modify the `DATABASE_URI` prefix in the application configuration.

1. Change `requirements.txt`:

   ```plaintext
   psycopg[binary]==3.1.12
   ```
2. Modify `DATABASE_URI` prefix in `config.py`, `test_models.py`, `test_service.py`, and `secret.yaml`:
   From `postgresql://` to `postgresql+psycopg://`.

<details>
<summary markdown="1">Detailed Explanation</summary>

This issue arises due to the absence or incompatibility of the PostgreSQL driver in your Python application. The solution involves updating to a newer PostgreSQL driver that is compatible with both Intel and ARM (Apple Silicon) platforms and doesn't require additional libraries.

Steps to resolve:

1. **Update `requirements.txt`**: Change the PostgreSQL driver version to `psycopg[binary]==3.1.12` in the `requirements.txt` file. This version of the driver is more up-to-date and compatible with a broader range of systems.

   ```plaintext
   psycopg[binary]==3.1.12
   ```
2. **Modify `DATABASE_URI` Prefix**: The database URI used in the application needs to be updated to work with the new driver. Change the prefix from `postgresql://` to `postgresql+psycopg://` in all instances within the application. This includes files like `config.py`, `test_models.py`, `test_service.py`, and any other place where `DATABASE_URI` is defined, including `secret.yaml`.
3. **Apply Changes**: After making these changes, ensure to install the updated dependencies using `pip install -r requirements.txt` or a similar command appropriate for your environment.
4. **Test the Application**: Run your test suites and start the application to ensure that the database connectivity issues are resolved.

This solution ensures compatibility across different platforms and simplifies the setup process by eliminating the need for additional libraries. The psycopg2-binary package provides a stand-alone package to interact with PostgreSQL.

</details>
