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
