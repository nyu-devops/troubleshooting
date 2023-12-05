Welcome to the NYU DevOps Troubleshooting Guide! This repository is dedicated to providing solutions and explanations for various DevOps-related issues. Whether you're encountering problems with Docker, Kubernetes, CI/CD processes, or other DevOps tools, this guide aims to help.

<form>
  <input type="text" id="search-term" style="width: 40vw;" >
  <button type="submit" onclick="search()">Search</button>  
</form>

<script>
  function search() {
  var searchTerm = document.getElementById("search-term").value;
  var url = "https://github.com/search?q=repo%3Anyu-devops%2Ftroubleshooting+" + searchTerm;
  window.open(url, '_blank');
}
</script>

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

---

We encourage TA's to contribute to this guide by sharing your solutions and insights. Together, we can build a comprehensive resource that benefits the entire NYU DevOps community. For contribution guidelines, please refer to the [CONTRIBUTING.md](CONTRIBUTING.md) file.

Thank you for visiting the DevOps Troubleshooting Guide. We hope you find the information here helpful and informative.
