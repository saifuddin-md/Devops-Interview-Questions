### 1. What is Docker?

- **Answer:**
Docker is a containerization platform that packages an application and its dependencies into a standardized unit called a container. Containers are isolated, portable, and lightweight compared with traditional virtual machines.

### 2. What is a Docker container?

**Answer:**

A container is a running instance of a Docker image. It provides an isolated environment in which an application and its dependencies can run.

### 3. What is a Dockerfile?**

**Answer:**

A Dockerfile is a text file containing instructions used to build a Docker image.

### 4. Explain common Dockerfile instructions.**

**Answer:**
- FROM → specifies the base image
- WORKDIR → sets the working directory
- COPY → copies files into the image
- ADD → copies files and supports additional features such as archives/URLs
- RUN → executes commands while building the image
- CMD → default command when the container starts
- ENTRYPOINT → defines the primary executable
- EXPOSE → documents intended container ports
- ENV → sets environment variables
- ARG → build-time variable
- USER → specifies the user to run as

### 5. What is the difference between CMD and ENTRYPOINT?

**Answer:**
CMD and ENTRYPOINT are both used to define what runs when a Docker container starts. The main difference is that CMD provides a default command or arguments, and it can be replaced with a different value/command when we run the container. but ENTRYPOINT generally can not be replaced by normal command-line arguments. 

We can also combine them—ENTRYPOINT defines the executable and CMD provides the default arguments.

**Example:**
if I have ENTRYPOINT ["python"] and CMD ["app.py"], the container runs python app.py by default. 
If I run the container with test.py, it runs python test.py. So, ENTRYPOINT is the fixed executable, while CMD provides the default arguments.

### 6 . What is the difference between COPY and ADD?

**Answer:**
- Both can copy files into an image.
- ADD has additional behavior, including automatic extraction of local tar archives.

### 7. Whst is Docker Layering?

**Answer:**

Docker layering is a mechanism where a Docker image is made up of multiple read-only and reusable layers. Each instruction in a Dockerfile typically creates a new layer. When a container runs, Docker adds a writable layer on top of these image layers. The main advantage is that Docker can cache and reuse unchanged layers, which makes image builds faster and reduces storage and download size.

**Example:**

If my Dockerfile has `FROM`, `RUN`, and `COPY` instructions, each creates a layer. If I only change the application code in the final `COPY` step, Docker can reuse the previous layers instead of rebuilding everything. This is why Docker layering improves build performance.

### 8. What is Docker Hub?

**Answer:**
Docker Hub is a container image registry where images can be stored and shared.


### 9. How do you check container logs?

**Answer:**
````bash
docker logs <container_id>
docker logs -f <container_id>
docker logs -t <container_id>
````

### 10. Why can 'docker exec -it -u appuser container bash' work even when 'appuser' has '/sbin/nologin' as its shell?

**Answer:**
Because docker exec directly starts bash as appuser. It doesn't perform a normal Linux login, so Docker doesn't 
use the user's configured login shell.

- /sbin/nologin prevents the user from getting an interactive (like ssh) login shell.

```bash
docker exec -it <container> bash   # directly starts bash
docker exec -it -u appuser <container> bash
```

### 11. How do you enter a running container?

**Answer:**
````bash
docker exec -it <container_id> /bin/bash
docker exec -it <container_id> /bin/sh
````

### 12. How do you inspect a container?

**Answer:**

````bash
docker inspect <container_id>
````
**This provides detailed configuration information such as:**

- IP address
- mounts
- environment
- network
- command
- container state

### 13. What is a Docker image?**

**Answer:**

A Docker image is a read-only template used to create containers. It contains application code, dependencies, libraries, and configuration.

**Q. How do you check Docker resource usage?**

**Answer:**
````bash
docker stats
````

**Q. How do you see Docker disk usage?**

**Answer:**
````bash
docker system df            # How to see total Docker disk usage
docker system prune -a      # This deletes all unused images/containers/networks.
````

**Q. How do you inspect the layers of a Docker image and identify which commands are creating large layers?**

**Answer:**
````bash
docker history <image-name>
````

**This shows:**

* Which command created each layer
* The size of each layer
* When the layer was created

It is useful for finding large layers and optimizing the Dockerfile.

**Q. Difference between EXPOSE and -p?**  

**Answer:**

EXPOSE documents which port the application uses inside the container. It does not publish the port to the host. -p actually maps and publishes a host port to a container port, making the application accessible from outside the container.

**Q. What are Docker volumes and bind mounts?**

**Answer:**
A Docker volume is storage managed by Docker and is commonly used for persistent application or database data.
A bind mount maps a specific file or directory from the host into the container and is commonly used during development when we want changes on the host to be reflected inside the container.

**1.** Docker Volume: (Managed by Docker)

```bash
docker run -v myvolume:/data myapp
```

**2.** Bind Mount: (Managed by us)

```bash
docker run -v /home/user/app:/app myapp      # Maps a specific host path to the container.
``` 

**Q. Why use Docker Compose?**

**Answer:**
Docker Compose is used to manage multiple Docker containers as one application. like frontend, backend, database. Instead of running each container manually, we define services, networks, volumes, and environment variables in a Compose yaml file and manage them using commands like docker compose up and docker compose down.

Without Compose, we might have to manually create networks, containers, volumes, environment variables, ports, etc.


**Q. How do you reduce Docker image size?**

**Answer:**

1.: Use Lightweight Base Images: using light base images like slim and alpine.

&#x20;

2.: Combine RUN Commands: Each RUN instruction creates a new layer. Combining them reduces the 

&#x20;   number of layers.


3\. Avoid Installing Recommended Packages: we Can avoid unnecessary packages from being installed. 

&#x20;  using --no-install-recommends command.


4.: Remove Package Cache: Package managers store cache which increases image size. We should remove 

&#x20;   it in the same layer., using rm -rf /var/lib/apt/list/\*.



5.: Scan and Optimize Images: we can use trivy and slim toolkit do reduce image size.



6.: Use Multi-Stage Builds: This removes build tools from the final image. Only the built application 

&#x20;   goes to final image.



7.: Use .dockerignore: Avoid copying unnecessary files.



**Example:**
```bash 

FROM python:3.11-slim

ENV PYTHONDONTWRITEBYTECODE=1

ENV PYTHONUNBUFFERED=1

WORKDIR /app

RUN apt-get update \&\& apt-get install -y --no-install-recommends \\

default-mysql-client \\

&#x20;   \&\& rm -rf /var/lib/apt/lists/\*



COPY requirements.txt .



RUN python -m pip install --upgrade pip \\

&#x20;\&\& python -m pip install --no-cache-dir -r requirements.txt



ARG UID=10001

RUN adduser \\

&#x20;   --disabled-password \\

&#x20;   --gecos "" \\

&#x20;   --home "/nonexistent" \\

&#x20;   --shell "/sbin/nologin" \\

&#x20;   --no-create-home \\

&#x20;   --uid "${UID}" \\

&#x20;   appuser



COPY --chown=appuser:appuser . .

USER appuser

EXPOSE 5000

CMD \["gunicorn", "-w", "2", "-b", "0.0.0.0:5000", "--access-logfile", "-", "--error-logfile", "-", "EmpApp:app"]

```

**Q. What is a multi-stage Docker build?**

**Answer:**
A multi-stage build uses multiple FROM statements so that build dependencies can be separated from the final runtime image.

**Example:**
````bash
FROM golang:1.24 AS builder
WORKDIR /app

COPY . .
RUN go build -o app
FROM alpine:latest

COPY --from=builder /app/app /app
CMD \["/app"]
````

* The final image contains the compiled application rather than the complete Go build environment.


**Q. What is .dockerignore?**

**Answer:**
.dockerignore prevents unnecessary files from being sent into the Docker build context.

**Example:**
````text
.git
node\_modules
.env
\*.log
README.md
````

**Q. Your container is consuming too much memory. What do you do?**

**Answer:**

I would first run docker stats to identify the container using too much memory. Then I would enter the container and use top or ps aux --sort=-%mem to find the process consuming the memory. I’d check the logs and application for issues like memory leaks, excessive caching, or too many workers. Finally, I’d set an appropriate memory limit and monitor the container.

````bash
docker stats   # Check memory usage

docker exec -it <container> sh

ps aux --sort=-%mem

docker inspect <container>    # Check the container's limit

docker logs <container>       # docker logs <container>

docker run --memory=512m myapp # Set a memory limit if appropriate
````


**Q. A container cannot connect to a database. What would you check?**

**Answer:**

First, I would check MONGO\_URI using printenv. Then I'd enter the backend container with docker exec and test connectivity to mongo:27017 using telnet. I'd also verify that mongo resolves correctly and that both containers are on the same Docker network. Finally, I'd check the MongoDB and backend logs and verify that MongoDB is actually running and ready to accept connections.

**Example:**

**a.** Check the MongoDB environment variable:-

**Inside the backend container:**
````bash
docker exec -it backend sh   # Enter the backend containe

printenv | grep MONGO        # mongo can be Docker service/hostname, while MONGO in grep MONGO is just a search term.

curl mongo:27017
````

**Q. A Docker build is very slow. How do you troubleshoot it?**

**Answer:**
I would inspect the \*\*Dockerfile and build context\*\* first. I’d run the build with detailed output to identify which layer is slow:

```bash
docker build --progress=plain .
```

**Then I’d check:**

\* \*\*Large build context\*\* — make sure `.dockerignore` excludes things like `.git`, `node\_modules`, build artifacts, and logs.

\* \*\*Docker layer caching\*\* — check whether frequently changing files are copied before expensive dependency-install steps.

\* \*\*Slow `RUN` commands\*\* — identify whether package installation, compilation, or downloads are taking the most time.

\* \*\*Cache invalidation\*\* — order the Dockerfile so stable dependencies are installed before frequently changing application code.

\* \*\*Docker/host resources\*\* — check CPU, memory, disk I/O, and network performance if the Dockerfile itself looks reasonable.


The goal is to identify the \*\*specific slow layer\*\* first, then optimize that part rather than guessing.



**Q. A container is running, but the application is unhealthy. What would you check?**

*I'd check:*


```bash

docker ps
docker logs <container>
docker inspect <container>
docker logs --tail 200 <container\_name>
```

I'd enter the container:

```bash 
docker exec -it <container\_name> sh
```

**Then:**

Verify the application is actually listening

```bash
docker exec <container\_name> netstat -lntp
```

**Q. How would you troubleshoot a production container that keeps restarting?**

**Answer:**

First I would check docker ps -a, then docker logs <container> to see the application error. I would use docker inspect to check the exit code, OOM status, error, and container state.

**Example:**

```bash
docker ps -a
docker logs <container>
docker inspect <container>
```

**Q. How do you copy a file from a container to the host?**

**Answer:**

```bash 
docker cp <container\_id>:/app/log.txt ./log.txt
```

**Q. How do you check why a container was killed?**

**Answer:**

**Check:**  

```bash
docker inspect <container\_id>
```

Look at the container state and exit information.

**Example:**

```bash
docker inspect --format='ExitCode={{.State.ExitCode}} OOMKilled={{.State.OOMKilled}}' <container>
```

If OOMKilled=true, the container was killed because of an out-of-memory condition.

**Then investigate:**

```bash
docker stats
```
and the host's available memory/system logs as appropriate.



**Q. What are Docker security best practices?**

**Answer:**
Important practices include:

* Don't run applications as root unnecessarily.
* Use trusted/minimal base images.
* Scan images for vulnerabilities.
* Don't store secrets in Dockerfiles.
* Keep images and dependencies updated.
* Use .dockerignore.
* Limit container resources where appropriate.
* Use read-only filesystems where practical.
* Minimize Linux capabilities.
* Don't expose unnecessary ports.
* Pin/verify important image versions or digests in controlled environments.
* Use least-privilege access to registries and deployment systems.

