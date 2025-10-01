# Lab 4 - CST8915 Full-stack Cloud-native Development: Introduction to Docker

Welcome to Lab 4 of the **CST8915 Full-stack Cloud-native Development** course. This lab will serve as an introduction to Docker to prepare you for containerizing applications in future labs. By the end of this lab, you will be familiar with Docker’s key concepts and basic commands, as well as able to create and run a simple containerized application.

## Learning Objectives

By the end of this lab, you will be able to:
- Set up an Azure VM as a Docker host
- Install Docker Engine using the apt repository
- Understand Docker's key concepts and architecture
- Work with Docker images, containers, and layers
- Create a Dockerfile and build custom images
- Understand how container layers work at runtime
- Use Docker Compose to manage multi-container applications

## Introduction to Docker
### What is Docker?
Docker is an open-source platform that enables developers to automate the deployment of applications inside lightweight, portable containers. These containers bundle the application with all of its dependencies and configurations, ensuring that the application runs consistently across different environments.

Key Concepts of Docker:

- **Containers:** Containers are lightweight, standalone executable packages of software that include everything needed to run the application: code, runtime, system tools, libraries, and settings.
- **Images:** An image is a read-only template used to create Docker containers. It contains everything needed to run an application. When you run a container, Docker uses an image as the base.
- **Dockerfile:** A text file that contains instructions on how to build a Docker image. The Dockerfile defines what goes into the image, including the base operating system, dependencies, environment variables, and the application code.
- **Docker Hub:** A cloud-based registry where Docker images can be stored and shared. You can push images to Docker Hub and pull them from there on different systems or cloud platforms.

**Docker Documentation:** The official Docker documentation is a comprehensive resource that provides detailed explanations, tutorials, and examples: [Docker Documentation](https://docs.docker.com/).

**Docker CLI Cheat Sheet:** This cheat sheet provides a quick reference for frequently used Docker commands: [Docker CLI Cheat Sheet](https://docs.docker.com/get-started/docker_cheatsheet.pdf)


## Prelab (Install and Run Docker locally):
Prelab is **optional** for this lab but highly recommended. 
### Step 1: Docker Desktop
Complete [Get Docker Desktop Guide (Video Included)](https://docs.docker.com/get-started/introduction/get-docker-desktop/)

### Step 2: Develop with containers
Complete [Develop with containers Guide (Video Included)](https://docs.docker.com/get-started/introduction/develop-with-containers/)

### Step 3: Build and push your first image
Complete [Build and push your first image (Video Included)](https://docs.docker.com/get-started/introduction/build-and-push-first-image/)

---

## Part 1: Setting Up Your Docker Host on Azure

### Step 1.1: Create an Ubuntu VM (Standard_B2s) on Azure
### Step 1.2: Connect to Your VM using VS Code
### Step 1.3: Install Docker Engine Using APT Repository
Follow the [Official Docker documentation](https://docs.docker.com/engine/install/ubuntu/) to install Docker on Ubuntu as followings:

**Update the package index:**
```bash
sudo apt-get update
```
*This refreshes the list of available packages and their versions from Ubuntu's repositories.*

**Install prerequisite packages:**
```bash
sudo apt-get install -y ca-certificates curl
```
*Installs SSL certificates for secure downloads and curl for fetching files from the internet.*

**Add Docker's official GPG key:**
```bash
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```
- *Creates a directory to store Docker's cryptographic signing key with proper permissions.*
- *Downloads Docker's GPG key to verify the authenticity of Docker packages.*
- *Makes the GPG key readable by all users on the system.*

**Set up the Docker repository:**
```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
*Adds Docker's official repository to your system's package sources for your specific Ubuntu version.*


**Update the package index again:**
```bash
sudo apt-get update
```
*Refreshes the package list to include packages from the newly added Docker repository.*

**Install Docker Engine, CLI, and plugins:**
```bash
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
*Installs Docker Engine (server), Docker CLI (command-line tool), containerd (container runtime), and Docker Compose plugin.*

### Step 1.4: Verify Docker Installation

**Check Docker version:**
```bash
sudo docker --version
```
*Displays the installed Docker version to confirm successful installation.*

**Run the hello-world container:**
```bash
sudo docker run hello-world
```
*Downloads and runs a test container that prints a confirmation message if Docker is working correctly.*

You should see a message confirming that Docker is working correctly.

**Add your user to the docker group (optional but recommended):**
```bash
sudo usermod -aG docker $USER
```
*Adds your user account to the docker group so you can run Docker commands without sudo.*


Then log out and log back in for the changes to take effect, or run:
```bash
newgrp docker
```

Now you can run Docker commands without `sudo`.

---

## Part 2: Docker Fundamentals

### Step 2.1: Understanding Docker Architecture

Docker uses a client-server architecture with three main components:

- **Docker Client**: The interface you use (docker CLI)
- **Docker Daemon**: The background service that manages containers
- **Docker Registry**: Storage for Docker images (e.g., Docker Hub)

### Step 2.2: Basic Docker Commands

**Check Docker system information:**
```bash
docker info
```
*Displays comprehensive system information including containers, images, storage driver, and Docker configuration.*

**List running containers:**
```bash
docker ps
```
*Shows all currently running containers with their status, ports, and names.*

**List all containers (including stopped ones):**
```bash
docker ps -a
```
*Shows all containers regardless of their state (running, stopped, or exited).*

**List downloaded images:**
```bash
docker images
```
*Displays all Docker images stored locally on your system with their size and tags.*

**Pull an image from Docker Hub:**
```bash
docker pull nginx:latest
```
*Downloads the latest NGINX web server image from Docker Hub to your local system.*

**Run a container:**
```bash
docker run -d -p 80:80 --name my-nginx nginx:latest
```
*Creates and starts an NGINX container in detached mode, mapping port 80 on host to port 80 in container.*

Breakdown of the command:
- `-d`: Run in detached mode (background)
- `-p 80:80`: Map port 80 on host to port 80 in container
- `--name my-nginx`: Assign a name to the container
- `nginx:latest`: The image to use
  - `NGINX` is a high-performance, lightweight web server that efficiently serves static content, acts as a reverse proxy, and handles load balancing for web applications.
  - `nginx` = the official NGINX image from Docker Hub.
  - `:latest` = the version tag. `latest` pulls the most recent version available.

**View container logs:**
```bash
docker logs my-nginx
```
*Displays all output (stdout/stderr) generated by the NGINX container, useful for debugging.*

**Execute commands inside a running container:**
```bash
docker exec -it my-nginx bash
```
*Opens an interactive bash shell inside the running container, allowing you to explore and run commands within it.*

Type `exit` to leave the container shell.

**Stop a container:**
```bash
docker stop my-nginx
```
*Gracefully stops the running container by sending a SIGTERM signal, allowing it to shut down cleanly.*

**Start a stopped container:**
```bash
docker start my-nginx
```
*Restarts a previously stopped container, preserving its configuration and data in the writable layer.*

**Remove a container:**
```bash
docker rm my-nginx
```
*Permanently deletes a stopped container and its writable layer (container must be stopped first).*

**Remove an image:**
```bash
docker rmi nginx:latest
```
*Deletes the NGINX image from your local system (no containers using this image can be running).*

---

## Part 3: Working with Dockerfiles

### Step 3.1: Understanding Dockerfiles

A Dockerfile is a text file containing instructions to build a Docker image. Each instruction creates a new layer in the image.

### Step 3.2: Create Your First Dockerfile

Create a new directory for your project:
```bash
mkdir ~/docker-lab
cd ~/docker-lab
```
*Creates a new directory for your Docker project and navigates into it.*

Create a simple Python application:
```bash
cat > app.py << 'EOF'
from flask import Flask
import os

app = Flask(__name__)

@app.route('/')
def hello():
    return f"Hello from Docker! Container ID: {os.uname().nodename}\n"

@app.route('/health')
def health():
    return "OK\n"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
EOF
```
*Creates a Flask web application with two routes: a hello message showing the container ID and a health check endpoint.*

Create a requirements file:
```bash
cat > requirements.txt << 'EOF'
Flask==3.0.0
Werkzeug==3.0.1
EOF
```
*Defines the Python package dependencies needed for the application with specific versions.*

Create a Dockerfile:
```bash
cat > Dockerfile << 'EOF'
# Use official Python runtime as base image
FROM python:3.11-slim

# Set working directory in container
WORKDIR /app

# Copy requirements file
COPY requirements.txt .

# Install Python dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY app.py .

# Expose port 5000
EXPOSE 5000

# Define environment variable
ENV FLASK_APP=app.py

# Run the application
CMD ["python", "app.py"]
EOF
```
*Creates a Dockerfile with instructions to build a containerized Flask application.*

### Step 3.3: Build the Docker Image

```bash
docker build -t my-python-app:v1 .
```
*Builds a Docker image from the Dockerfile in the current directory and tags it as my-python-app version 1.*

Breakdown:
- `-t my-python-app:v1`: Tag the image with name and version
- `.`: Build context (current directory)

**View the newly built image:**
```bash
docker images
```
*Lists all local images including your newly built my-python-app image.*

### Step 3.4: Run Your Containerized Application

```bash
docker run -d -p 80:5000 --name python-app my-python-app:v1
```
*Starts your Flask application in a container, mapping host port 80 to container port 5000.*

**Test the application:**
```bash
curl http://localhost:80
curl http://localhost:80/health
```
- *Sends an HTTP request to the root endpoint to see the hello message.*
- *Checks the health endpoint to verify the application is running correctly.*


**View container details:**
```bash
docker inspect python-app
```
*Displays comprehensive JSON-formatted information about the container including network settings, mounts, and configuration.*

---

## Part 4: Understanding Docker Images and Layers

### Step 4.1: Image Layers Explained

Docker images are built in layers. Each instruction in a Dockerfile creates a new read-only layer. This layered approach provides several benefits:

- **Efficiency**: Layers are cached and reused
- **Speed**: Only changed layers need to be rebuilt
- **Storage**: Common layers are shared between images

### Step 4.2: Inspect Image Layers

**View the history of an image:**
```bash
docker history my-python-app:v1
```
*Shows each layer in the image, the Dockerfile instruction that created it, and the size of each layer.*

**View detailed layer information:**
```bash
docker inspect my-python-app:v1
```
*Displays detailed JSON metadata about the image including all layer IDs and configuration.*

Look for the `"Layers"` section in the output.

### Step 4.3: Understanding Layer Caching

Modify your Dockerfile to demonstrate caching (This is identical to previous Dockerfile, just removed comments):

```bash
cat > Dockerfile << 'EOF'
FROM python:3.11-slim
WORKDIR /app

# These layers will be cached if requirements.txt doesn't change
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# This layer changes when app.py changes
COPY app.py .

EXPOSE 5000
ENV FLASK_APP=app.py
CMD ["python", "app.py"]
EOF
```

**Rebuild the image:**
```bash
docker build -t my-python-app:v2 .
```

Notice how Docker uses cached layers. Now modify `app.py` slightly:

```bash
sed -i 's/Hello from Docker!/Hello from Docker v2!/' app.py
```
*Modifies the hello message in app.py to trigger a layer rebuild.*

**Build again:**
```bash
docker build -t my-python-app:v3 .
```
*Rebuilds the image, showing that only layers after the changed file are rebuilt (cache is used for earlier layers).*

Observe that only the layers after the changed file are rebuilt.

---

## Part 5: How Layers Work at Runtime

### Step 5.1: Container Writable Layer

When you run a container, Docker adds a thin writable layer on top of the read-only image layers. This is called the **container layer**.

**Key concepts:**
- All read operations go through all layers (Union File System)
- Write operations only happen in the container layer
- When a container is deleted, the writable layer is lost
- Multiple containers from the same image share the read-only layers

### Step 5.2: Demonstrate Separate Writable Layers

**Run multiple containers from the same image:**
```bash
docker run -d -p 5001:5000 --name app1 my-python-app:v3
docker run -d -p 5002:5000 --name app2 my-python-app:v3
docker run -d -p 5003:5000 --name app3 my-python-app:v3
```
- *Starts the first container instance mapping to port 5001.*
- *Starts the second container instance mapping to port 5002.*
- *Starts the third container instance mapping to port 5003 - all three share the same image layers.*

**Access each container and create a unique file:**

In container 1:
```bash
docker exec app1 sh -c "echo 'Data in container 1' > /app/data1.txt"
```
*Creates a unique file in app1's writable layer that exists only in this container.*

In container 2:
```bash
docker exec app2 sh -c "echo 'Data in container 2' > /app/data2.txt"
```
*Creates a unique file in app2's writable layer that exists only in this container.*

In container 3:
```bash
docker exec app3 sh -c "echo 'Data in container 3' > /app/data3.txt"
```
*Creates a unique file in app3's writable layer that exists only in this container.*

**Verify that each container has only its own data:**
```bash
docker exec app1 ls /app
docker exec app2 ls /app
docker exec app3 ls /app
```
- *Lists files in app1 - shows app.py, requirements.txt, and data1.txt.*
- *Lists files in app2 - shows app.py, requirements.txt, and data2.txt.*
- *Lists files in app3 - shows app.py, requirements.txt, and data3.txt.*

Each container has its own writable layer with its unique file.

**Check disk usage by container:**
```bash
docker ps -s
```
*Shows container sizes including the writable layer size for each container.*

The `SIZE` column shows the writable layer size for each container.

### Step 5.3: Copy-on-Write (CoW) Strategy

When a container needs to modify a file from a read-only layer:

1. Docker copies the file to the container's writable layer
2. The modification happens in the copy
3. The original file in the image layer remains unchanged

**Demonstrate CoW:**
```bash
docker exec app1 sh -c "echo 'Modified app' >> /app/app.py"
docker exec app1 cat /app/app.py
docker exec app2 cat /app/app.py
```
- *Modifies app.py in app1, triggering copy-on-write (the file is copied to app1's writable layer before modification).*
- *Shows the modified version of app.py in app1.*
- *Shows the original unchanged version of app.py in app2 (still reading from the read-only image layer).*


Notice that `app1` has the modified version, but `app2` still has the original.

---

## Part 6: Docker Compose for Multi-Container Applications

### Step 6.1: Understanding Docker Compose

Docker Compose is a tool for defining and running multi-container applications. You use a YAML file to configure your application's services, networks, and volumes.

### Step 6.2: Create a Multi-Container Application

Create a new directory:
```bash
mkdir ~/docker-compose-lab
cd ~/docker-compose-lab
```
*Creates a dedicated directory for the Docker Compose lab and navigates into it.*

Create a simple web application with Redis:

**app.py:**
```bash
cat > app.py << 'EOF'
from flask import Flask
import redis
import os

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)

@app.route('/')
def hello():
    count = cache.incr('hits')
    return f'Hello! This page has been visited {count} times.\n'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
EOF
```
*Creates a Flask application that uses Redis to count and display page visits.*

**requirements.txt:**
```bash
cat > requirements.txt << 'EOF'
Flask==3.0.0
redis==5.0.1
Werkzeug==3.0.1
EOF
```
*Lists Python dependencies including Flask and the Redis client library.*

**Dockerfile:**
```bash
cat > Dockerfile << 'EOF'
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY app.py .
EXPOSE 5000
CMD ["python", "app.py"]
EOF
```
*Defines how to build the Docker image for the Flask application.*

### Step 6.3: Create a Docker Compose File

```bash
cat > docker-compose.yml << 'EOF'
version: '3.8'

services:
  web:
    build: .
    ports:
      - "80:5000"
    depends_on:
      - redis
    environment:
      - FLASK_ENV=development
    networks:
      - app-network

  redis:
    image: redis:7-alpine
    networks:
      - app-network
    volumes:
      - redis-data:/data

networks:
  app-network:
    driver: bridge

volumes:
  redis-data:
EOF
```
*Creates a Docker Compose configuration defining two services (web and redis) with their networking and storage.*

### Step 6.4: Run the Application with Docker Compose

**Start the application:**
```bash
docker compose up -d
```
*Builds images, creates networks/volumes, and starts all services defined in docker-compose.yml in detached mode.*

This command:
- Builds the web service image
- Pulls the Redis image
- Creates a network
- Creates a volume
- Starts both containers

**View running services:**
```bash
docker compose ps
```
*Lists all services managed by Docker Compose with their status, ports, and health.*

**Test the application:**
```bash
curl http://localhost:80
curl http://localhost:80
curl http://localhost:80
```
- *Visits the page for the first time - the counter should show 1.*
- *Visits the page again - the counter increments to 2.*
- *Third visit - the counter increments to 3, demonstrating Redis persistence.*


Notice the hit counter incrementing.

**View logs:**
```bash
docker compose logs
docker compose logs web
docker compose logs redis
```
- *Displays combined logs from all services (both web and redis).*
- *Shows logs only from the web service.*
- *Shows logs only from the redis service.*

**Follow logs in real-time:**
```bash
docker compose logs -f
```
*Streams live logs from all services, updating as new log entries are generated.*

### Step 6.5: Docker Compose Commands

**Stop the application:**
```bash
docker compose stop
```
*Stops all running containers managed by Docker Compose without removing them.*

**Start the stopped application:**
```bash
docker compose start
```
*Restarts previously stopped containers with their existing configuration and data.*

**Restart services:**
```bash
docker compose restart
```
*Stops and then immediately starts all services (useful for applying configuration changes).*

**Stop and remove containers, networks:**
```bash
docker compose down
```
*Stops and removes all containers and networks, but preserves volumes.*

**Stop and remove containers, networks, and volumes:**
```bash
docker compose down -v
```
*Stops and removes all containers, networks, AND volumes (permanently deletes all data).*

**Scale a service:**
Update `docker-compose.yml` 
```bash
cat > docker-compose.yml << 'EOF'
version: '3.8'

services:
  web:
    build: .
    ports:
      - "5000"
    depends_on:
      - redis
    environment:
      - FLASK_ENV=development
    networks:
      - app-network

  redis:
    image: redis:7-alpine
    networks:
      - app-network
    volumes:
      - redis-data:/data

networks:
  app-network:
    driver: bridge

volumes:
  redis-data:
EOF
```
*Modifies docker-compose.yml to use dynamic port mapping (removes fixed port 80) to allow scaling.*

Then run:
```bash
docker compose up -d --scale web=3
```
*Starts 3 instances of the web service, with Docker automatically assigning different host ports to each.*

**Execute a command in a running service:**
```bash
docker compose exec web sh
```
*Opens an interactive shell in one of the web service containers for debugging or inspection.*


---

## Part 7: Clean Up

### Step 7.1: Remove All Lab Resources

**Stop all running containers:**
```bash
docker stop $(docker ps -aq)
```
*Stops all running Docker containers by passing their IDs to the stop command.*

**Remove all containers:**
```bash
docker rm $(docker ps -aq)
```
*Deletes all containers (both stopped and exited) from your system.*

**Remove all images:**
```bash
docker rmi $(docker images -q)
```
*Removes all Docker images from your local system to free up disk space.*

**Remove all volumes:**
```bash
docker volume prune -f
```
*Deletes all unused volumes without prompting for confirmation.*

**Remove all networks:**
```bash
docker network prune -f
```
*Removes all custom networks not being used by any containers.*

**System-wide cleanup:**
```bash
docker system prune -a --volumes -f
```
*Performs a comprehensive cleanup: removes all stopped containers, unused networks, dangling images, and volumes.*

---

## Additional Resources

- [Docker Official Documentation](https://docs.docker.com/)
- [Docker Hub](https://hub.docker.com/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [Dockerfile Best Practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

---

## Troubleshooting

**Issue**: Cannot connect to Docker daemon
```bash
sudo systemctl status docker
sudo systemctl start docker
```
- *Checks if the Docker daemon is running.*
- *Starts the Docker daemon if it's stopped.*


**Issue**: Permission denied while trying to connect to Docker
```bash
sudo usermod -aG docker $USER
newgrp docker
```
*Adds your user to the docker group and activates the membership.*

**Issue**: Port already in use
```bash
# Find what's using the port
sudo lsof -i :5000
# Stop the container using that port
docker stop <container-name>
```
- *Lists processes using port 5000 to identify conflicts.*
- *Stops the container that's occupying the port.*


## Submission

### What to Submit
1. **Demo Video (Max 5 minutes)**  
   - Record a short demo video for **Part 6 only**

2. **Reflection Questions**  
   Answer the following in your `README.md` file. Keep your answers **short but thoughtful** (≈1 short paragraph each).  
    1. What are the main differences between a Docker image and a Docker container?
    2. Explain how Docker's layered architecture improves efficiency.
    3. Why does each container get its own writable layer?
    4. What are the benefits of using Docker Compose over running containers individually?

3. **GitHub Repository (Submission Repo)**  
   - You must create **one GitHub repository** for your lab submission.  
   - This submission repository must include:  
     - A `README.md` file with:  
       - The YouTube demo video link.  
       - Your written answers to the Reflection Questions.  
       - Links to the three service repositories you created in lab 2:   
     - (Optional) Notes about setup challenges or lessons learned.  

### How to Submit
- Push your work to a **public GitHub repository** (the submission repository).  
- Submit the link to your submission repository as your final lab deliverable in **Brightspace**.  


