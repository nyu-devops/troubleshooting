# Docker Issues

- **Clean Docker Environment**: Sometimes, Docker can cache images or configurations that cause repeated errors. To ensure a fresh environment, the following Docker commands can be used to clean up:
    1. `docker kill $(docker ps -q)`: Stops all running containers.
    2. `docker rm $(docker ps -aq)`: Removes all containers.
    3. `docker system prune --volumes -f`: Removes all stopped containers, unused networks, images, and optionally, volumes.
    4. `docker volume rm $(docker volume ls -q)`: Removes all unused volumes.

---

### Problem Statement:

While attempting to build a Docker image, the process fails when trying to install the **`psycopg2`** package, indicating that **`pg_config`** is missing. The user has verified that the **`requirements.txt`** matches the one provided in the TDD lab, yet the issue persists.

### Immediate Solution:

Update [Dockerfile](https://github.com/nyu-devops/lab-flask-bdd/blob/c4654d806cdcc6e65f50126c050f4c52b3b5583f/Dockerfile#L23C1-L23C1)  to include `RUN apt-get update && apt-get install -y postgresql-client` on Line 23 which are required to build the **`psycopg2`** package. 

Alternatively, switch to using the binary package **`psycopg[binary]`** to avoid the need for compiling the package and installing additional dependencies.


---