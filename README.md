Cgroups, namespaces, and beyond: what are containers made from?

https://youtu.be/sK5i-N34im8?si=Db5HG2LRPgAggBsm

## Hypervisor:

- A hypervisor is software that pools computing resources—like processing, memory, and storage—and reallocates them among virtual machines (VMs). 
- This technology makes virtualization possible, meaning you can create and run many VMs from a single physical machine.

![image](https://github.com/user-attachments/assets/5c952809-afaf-46e2-bdf4-692c913ad455)

![image](https://github.com/user-attachments/assets/1b0c44bf-a48c-4831-8343-28c3e6d23637)

https://www.redhat.com/en/topics/virtualization/what-is-a-hypervisor

### VM vs Container

![image](https://github.com/user-attachments/assets/103d4a90-e594-4fd0-868b-230ea3fa0d12)

![image](https://github.com/user-attachments/assets/b1fcd9a4-8712-4ee9-b455-5af6b3512b67)

# Top Docker Commands

Check Docker version:
```sh
docker --version
```

Pull an image (replace "image_name" with the actual image name):
```sh
docker pull image_name
```

Run a container in detached mode (background):
```sh
docker run -d image_name
```

Run a container with a custom name (replace "container_name" and "image_name"):
```sh
docker run --name container_name image_name
```

Run a container interactively (get a terminal inside):
```sh
docker run -it image_name
```

List all running containers:
```sh
docker ps
```

List all containers (running and stopped):
```sh
docker ps -a
```

Stop a running container (replace "container_name" or "container_id"):
```sh
docker stop container_name  # OR  docker stop container_id
```

Remove a stopped container (replace "container_name" or "container_id"):
```sh
docker rm container_name  # OR  docker rm container_id
```

**Image Management:**

List all images:
```sh
docker images
```

Remove an image (replace "image_name" or "image_id"):
```sh
docker rmi image_name  # OR  docker rmi image_id
```

**for deleting unused images**

```sh
system prune -a -f
```
removes unused images, not necessarily dangling images (untagged layers). If you want to remove dangling images as well, use the ```-a```


# Docker-Study

* Docker is written in the 'GO' language.
* Docker is a tool that performs OS-level virtualization, also known as containerization.

### Docker on Linux 

### Docker on Windows

### Dockerfile

```Dockerfile
FROM node:12.2.0-alpine
WORKDIR app
COPY . .
RUN npm install
RUN npm run test
EXPOSE 8000
CMD ["node","app.js"]
```


#### ///////////////// Dockerfile-1 ////////////////////////////////

```Dockerfile FROM python:3.9-buster

WORKDIR /app

COPY requirements.txt .

RUN pip3 install --no-cache-dir -r requirements.txt

COPY . .

ENV FLASK_RUN_HOST=0.0.0.0

EXPOSE 5000

CMD ["flask", "run"]
```



#### ///////////////// Dockerfile-2 ////////////////////////////


```Dockerfile
# First Stage: Build Stage
FROM openjdk:8-jdk as build
 
# Working Directory where all code will be kept
WORKDIR /app/
 
# Copy the app to the current directory of the build stage
COPY Hello.java .
 
# Compile code
RUN javac Hello.java
 
# Second Stage: Runtime Stage
FROM openjdk:8-jdk-alpine
 
# Set the working directory for the second stage
WORKDIR /app/
 
# Copy the compiled code from the build stage to the runtime stage
COPY --from=build /app/ .
 
# Run java compiled code
CMD ["java", "Hello"]
```



#### ////////////////////////// Dockerfile-3 ///////////////////////

```Dockerfile
FROM python:3.12.0b4-slim-bullseye

WORKDIR /app

COPY . .

RUN pip3 install --no-cache-dir -r requirements.txt

ENV FLASK_RUN_HOST=0.0.0.0

EXPOSE 5000

CMD ["flask", "run"]
```

#### ////////////////////////// Dockerfile-4 ///////////////////////
Multistage Docker Build 3-stage
```Dockerfile
# Stage 1: Base
FROM python:3.9-slim-buster AS base
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Stage 2: Build
FROM base AS build
COPY . .

# Stage 3: Final
FROM python:3.9-slim-buster AS final
WORKDIR /app
COPY --from=build /app /app
CMD ["flask", "run"]
```

![Dockerfile-instructions](https://github.com/user-attachments/assets/c18160b9-04e8-43c3-bd33-2a49911315c3)

## Docker Volume

The default Docker volume storage path:

`/var/lib/docker/volumes`

**Root Directory:**

Docker stores all its data, including images, containers, volumes, and networks, within the `/var/lib/docker` directory. 

## Docker Compose

**--build-context** ```--build-context stringArray Additional build contexts (e.g., name=path)```

```sh
docker build -t sample-site -f sample-site/docker/Dockerfile --build-context sample-site/ .
```
In this command, --build-context sample-site/ specifies that the build context should be the sample-site/ directory, which includes the html/ and docker/ directories. The . at the end of the command specifies the current directory as the build context for files that are not in the sample-site/ directory (like the config/ directory).

It’s a common practice to keep the Dockerfile at the project root directory. The command, by default, expects the Dockerfile to be present there. All the files we want to include in the image should exist somewhere inside that context.

General Dockerfile folder structurea:

```
project-root/
│
├── Dockerfile
├── app/
│   ├── src/
│   │   └── (application source files)
│   ├── static/
│   │   └── (static files)
│   └── templates/
│       └── (HTML template files)
│
├── config/
│   └── (configuration files)
│
├── tests/
│   └── (test scripts and data)
│
└── README.md
```

If the structure is like this 

```
projects/
├── <some other projects>...
├── sample-site/
│   ├── html/
│   │   └── index.html
│   └── docker/
│       └── Dockerfile
└── config/
    └── nginx.conf
```

```sh
docker build -t sample-site -f sample-site/docker/Dockerfile .
```

```sh
def dockerfilePath = fileExists('Dockerfile') ? 'Dockerfile' : 'KYC/monitor/Dockerfile'
sh "docker buildx build -t ${CONTAINER_REGISTRY_URL}/${ECR_REPO_NAME}:amd-${VERSION} -f ${dockerfilePath} ${buildContext} --build-arg PACKAGE_READ_TOKEN=${PACKAGE_READ_TOKEN} --build-arg BUILD_VERSION=${VERSION} --build-arg GIT_COMMIT=${scmVars.GIT_COMMIT[0..7]}"
```

docker build with --build-arg with multiple arguments

Use ```--build-arg``` with each argument.
If you are passing two argument then add ```--build-arg``` with each argument like:

```Dockerfile
docker build \
-t essearch/ess-elasticsearch:1.7.6 \
--build-arg number_of_shards=5 \
--build-arg number_of_replicas=2 \
--no-cache .
```

https://kodekloud.com/blog/docker-build-args/

https://stackoverflow.com/questions/42297387/docker-build-with-build-arg-with-multiple-arguments

# How to Reduce Docker Image Size

https://github.com/vaibhavkapase1302/Docker-Study/blob/main/Books/How%20to%20reduce%20docker%20image%20size.pdf

Reducing Docker image sizes is crucial for optimizing container deployment, enhancing scalability, and minimizing storage costs. Here's how you can effectively reduce Docker image sizes:

### 1. **Use Official Minimal Base Images:**
   - **Choose Lightweight Versions:** Start with minimal base images like `python:3.9-slim` or `python:3.9-alpine` instead of full-sized OS images. For instance, `python:3.9-alpine` is significantly smaller (around 95.2% smaller) than `python:3.9`.

### 2. **Minimize Layers:**
   - **Combine Commands:** Each command in a Dockerfile creates a new layer, increasing the image size. Combine similar commands to reduce layers.
   - **Example:**
     ```Dockerfile
     # Instead of this
     RUN apk update
     RUN apk add --no-cache git
     RUN rm -rf /var/cache/apk/*
     
     # Do this
     RUN apk update && apk add --no-cache git && rm -rf /var/cache/apk/*
     ```

### 3. **Use .dockerignore File:**
   - **Exclude Unnecessary Files:** Use a `.dockerignore` file to prevent unnecessary files and directories from being copied into the image, reducing the final image size.
   - **Sample .dockerignore:**
     ```
     __pycache__
     *.pyc
     *.pyo
     *.pyd
     venv/
     ```

### 4. **Multi-Stage Builds:**
   - **Separate Build and Runtime Stages:** Multi-stage builds allow you to keep only the essential parts of the application in the final image, drastically reducing its size.
   - **Example:**
     ```Dockerfile
     # Stage 1: Build
     FROM python:3.9-alpine AS builder
     RUN apk add --no-cache build-base gfortran musl-dev lapack-dev
     WORKDIR /app
     COPY requirements.txt ./
     RUN pip install --no-cache-dir -r requirements.txt
     COPY . .
     
     # Stage 2: Production
     FROM python:3.9-alpine
     WORKDIR /app
     COPY --from=builder /app /app
     EXPOSE 5000
     CMD ["python", "app.py"]
     ```
   - **Result:** The image size drops from 588 MB (single-stage) to 47.7 MB (multi-stage).

### 5. **Use Static Binaries and 'scratch' Base Image:**
   - **Empty Base Image:** If your application is a static binary, use the `scratch` base image, which is empty and results in a very small final image.
   - **Example:**
     ```Dockerfile
     FROM scratch
     COPY myapp /
     CMD ["/myapp"]
     ```

### **Security Considerations:**
   - **Use Trusted and Official Base Images:** Always start with verified images to ensure security.
   - **Run Containers as Non-Root Users:** Reduce the risk of privilege escalation by running containers with non-root users.
   - **Regular Vulnerability Scans:** Regularly scan Docker images for vulnerabilities using tools like `Clair` or `Trivy`.
   - **Limit Network Exposure:** Restrict ports and IP addresses to reduce attack surfaces.
     ```bash
     docker run -p 127.0.0.1:8080:8080 myimage
     ```
   - **Avoid Hardcoding Sensitive Information:** Never hardcode secrets in Dockerfiles. Use environment variables or secret management tools.

### **Final Reminder:**
- **Smaller Image Size = Faster Deployments + Quicker Scaling + Leaner Infrastructure**

By following these practices, you can significantly reduce Docker image sizes, improving both performance and security.

### Difference between the 'COPY' and 'ADD' commands in a Dockerfile?

https://stackoverflow.com/questions/24958140/what-is-the-difference-between-the-copy-and-add-commands-in-a-dockerfile


## Docker Network

https://docs.docker.com/engine/network/

https://spacelift.io/blog/docker-networking#docker-network-types

https://docs.docker.com/reference/cli/docker/network/

### Docker network types & use cases

Docker supports six network types to manage container communication that implement core networking functionality:

1. `bridge:` The default for standalone containers. It creates a private internal network on the host, and containers can communicate through it using IPs or container names.
2. `host:` Removes network isolation by using the host’s network stack directly. This allows containers to share the host’s IP and ports, which is useful for performance or compatibility needs.
3. `none:` Disables networking completely. Useful for security or manual configuration.
4. `overlay:` Enables multi-host networking using Docker Swarm. It creates a distributed network across nodes, allowing containers on different hosts to communicate securely.
5. `macvlan:` Assigns a MAC address to each container, making it appear as a physical device on the network. Used for scenarios requiring full network integration, such as legacy apps.
6. `ipvlan:` Similar to macvlan but uses a different method for traffic handling. It’s more efficient for high-density environments but less flexible.

end!!
