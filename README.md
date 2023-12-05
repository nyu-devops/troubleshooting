# DevOps Troubleshooting Guide

Welcome to the NYU DevOps Troubleshooting Guide! This repository is dedicated to providing solutions and explanations for various DevOps-related issues. Whether you're encountering problems with Docker, Kubernetes, CI/CD processes, or other DevOps tools, this guide aims to help.

### General Solutions to Try

Before diving into specific issues, here are some general solutions that might resolve common problems:

- **Restart VSCode**: Sometimes, simply closing and reopening Visual Studio Code can resolve unexpected issues.

- **Clean Docker Environment**: Docker can sometimes cache images or configurations that cause repeated errors. To ensure a fresh environment, use the following Docker commands to clean up:
    1. `docker kill $(docker ps -q)`: Stops all running containers.
    2. `docker rm $(docker ps -aq)`: Removes all containers.
    3. `docker system prune --volumes -f`: Removes all stopped containers, unused networks, images, and optionally, volumes.
    4. `docker volume rm $(docker volume ls -q)`: Removes all unused volumes.

    ```bash
    docker kill $(docker ps -q)
    docker rm $(docker ps -aq)
    docker system prune --volumes -f
    docker volume rm $(docker volume ls -q)
    ```

---

### Troubleshooting Sections

Explore the following sections for more specific troubleshooting guides:

- [General Issues](content/General.md): General issues with the Repository
- [BDD Issues](content/BDD.md): Guidance on issues related to Behavior-Driven Development.
- [Kubernetes Issues](content/Kubernetes.md): Solutions for common Kubernetes challenges.
- [Docker Issues](content/Docker.md): Help with Docker-related problems.
- [CI/CD Issues](content/CICD.md): Assistance with Continuous Integration and Continuous Deployment processes.
- [Git Issues](content/Git.md): Tips for resolving issues related to Git.
- [OpenShift Issues](content/OpenShift.md) : Issues faced with OpenShift

---

We encourage TA's to contribute to this guide by sharing your solutions and insights. Together, we can build a comprehensive resource that benefits the entire NYU DevOps community. For contribution guidelines, please refer to the [CONTRIBUTING.md](CONTRIBUTING.md) file.

Thank you for visiting the DevOps Troubleshooting Guide. We hope you find the information here helpful and informative.