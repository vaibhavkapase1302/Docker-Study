Cgroups, namespaces, and beyond: what are containers made from?

https://youtu.be/sK5i-N34im8?si=Db5HG2LRPgAggBsm

## Hypervisor:

- A hypervisor is software that pools computing resources—like processing, memory, and storage—and reallocates them among virtual machines (VMs). 
- This technology makes virtualization possible, meaning you can create and run many VMs from a single physical machine.
- e.g. Oracle VirtualBox

![image](https://github.com/user-attachments/assets/5c952809-afaf-46e2-bdf4-692c913ad455)

![image](https://github.com/user-attachments/assets/1b0c44bf-a48c-4831-8343-28c3e6d23637)

https://www.redhat.com/en/topics/virtualization/what-is-a-hypervisor

### VM vs Container

![image](https://github.com/user-attachments/assets/103d4a90-e594-4fd0-868b-230ea3fa0d12)

![image](https://github.com/user-attachments/assets/b1fcd9a4-8712-4ee9-b455-5af6b3512b67)

## Docker Arch

<img width="3178" height="1734" alt="image" src="https://github.com/user-attachments/assets/561e2662-1358-4612-8de9-5e59a5cb2d55" />

<img width="2748" height="1730" alt="image" src="https://github.com/user-attachments/assets/198893ec-9f04-4f2c-9398-4d9de753b5c1" />

<img width="3318" height="1528" alt="image" src="https://github.com/user-attachments/assets/339e9d08-0051-4f1e-ae70-294c9b070664" />


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

<img width="2460" height="1356" alt="image" src="https://github.com/user-attachments/assets/ef151cd1-136a-4cf3-9fef-20bc1635896a" />

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

Docker networking enables **communication** between containers, between containers and the host, and sometimes between containers across different Docker hosts.

Containers are isolated by default but can be connected using various network drivers.

<img width="3128" height="1786" alt="image" src="https://github.com/user-attachments/assets/d0eebf7a-c485-4c06-b193-6edaf74d7b36" />

https://docs.docker.com/engine/network/

https://spacelift.io/blog/docker-networking#docker-network-types

https://docs.docker.com/reference/cli/docker/network/

Absolutely! Let’s dive deep into **“How Docker Networking Works Under the Hood”** so you get a solid understanding of the internals.


#### How Docker Networking Works Under the Hood

Docker networking relies heavily on **Linux kernel features** to provide isolation and connectivity between containers. The main building blocks are:

##### 1. **Linux Network Namespaces**

* A **network namespace** is a lightweight, isolated network stack for a group of processes.
* When Docker creates a container, it creates a **separate network namespace** for it.
* This namespace has its own interfaces, routing tables, firewall rules, and network devices — completely isolated from other namespaces (including the host’s default namespace).
* This means a container can have its own IP address and network configuration independent from the host or other containers.


##### 2. **Virtual Ethernet Pairs (`veth`)**

* To connect container network namespaces to the Docker host network, Docker uses **virtual Ethernet (veth) pairs**.
* A **veth pair** acts like a virtual network cable: packets sent on one end appear on the other end.
* When a container is created:

  * One end of the veth pair is placed inside the container’s network namespace (usually named `eth0`).
  * The other end remains in the host’s default network namespace and is attached to a Docker network bridge (e.g., `docker0`).


#### 3. **Docker Bridge Network (`docker0`)**

* The default Docker bridge (`docker0`) is a virtual Ethernet bridge created on the host.
* It acts like a virtual switch, connecting all container-side veth interfaces attached to the bridge.
* Containers connected to the same bridge network can communicate directly using their internal IP addresses.
* The bridge also assigns IP addresses from a private subnet to connected containers.


##### 4. **IP Address Assignment**

* Docker uses an internal **IP address management (IPAM)** system to allocate IP addresses to containers.
* When a container joins a network (bridge by default), it gets assigned an IP address from the subnet defined for that network.
* Containers use this IP address within the network namespace for communication.


##### 5. **Network Address Translation (NAT) and Port Forwarding**

* Containers on the bridge network have private IPs that are not directly reachable outside the host.
* Docker uses **iptables** rules on the host to perform **Network Address Translation (NAT)**, allowing containers to access external networks (internet).
* For external access to containers (e.g., web servers), Docker maps host ports to container ports (port forwarding) using iptables rules.
* This allows you to expose container services on the host IP and ports.


##### 6. **Routing and Firewall**

* Docker modifies the host’s IP routing tables and firewall (iptables) rules to:

  * Enable container-to-container communication on the same network.
  * Enable container-to-host and container-to-external network communication.
* Docker automatically manages these rules as containers start and stop.


##### 7. **User-Defined Networks**

* When you create a **user-defined bridge network**, Docker creates a new bridge interface with its own subnet and routing.
* Containers on the same user-defined network can resolve each other by name via embedded DNS, simplifying communication.


##### Visual Summary (Conceptual): ✅ IMP

```
Host Network Namespace
    ├── docker0 (bridge) <--- virtual switch
    │     ├── veth-host-1 (host end of veth pair)
    │     ├── veth-host-2
    │     └── ...
    |
    ├── Container 1 Namespace
    │     └── eth0 (container end of veth pair)
    |
    ├── Container 2 Namespace
    │     └── eth0
    |
    └── Routing & iptables for NAT & port forwarding
```

##### Bonus: Why is this Important?

* This isolation and networking model is what makes containers lightweight yet securely isolated.
* It enables flexible container-to-container networking on the same host without IP conflicts.
* You can extend these concepts for multi-host networking using overlay networks.


### Docker network types & use cases

Docker supports six network types to manage container communication that implement core networking functionality:

1. `bridge:` The default for standalone containers. It creates a private internal network on the host, and containers can communicate through it using IPs or container names.
2. `host:` Removes network isolation by using the host’s network stack directly. This allows containers to share the host’s IP and ports, which is useful for performance or compatibility needs.
3. `none:` Disables networking completely. Useful for security or manual configuration.
4. `overlay:` Enables multi-host networking using Docker Swarm. It creates a distributed network across nodes, allowing containers on different hosts to communicate securely.
5. `macvlan:` Assigns a MAC address to each container, making it appear as a physical device on the network. Used for scenarios requiring full network integration, such as legacy apps.
6. `ipvlan:` Similar to macvlan but uses a different method for traffic handling. It’s more efficient for high-density environments but less flexible.


#### 5. Basic Commands

* List networks:

  ```bash
  docker network ls
  ```

* Inspect a network:

  ```bash
  docker network inspect <network-name>
  ```

* Create a network:

  ```bash
  docker network create --driver bridge my_bridge_network
  ```

* Connect a container to a network:

  ```bash
  docker network connect <network-name> <container-name>
  ```

* Disconnect a container from a network:

  ```bash
  docker network disconnect <network-name> <container-name>
  ```

#### 6. Example: Run two containers on a user-defined bridge network

```bash
docker network create my_net

docker run -dit --name container1 --network my_net alpine sh

docker run -dit --name container2 --network my_net alpine sh
```

Inside container1, you can ping container2 by name:

```bash
docker exec container1 ping container2
```

**Port Mapping**

- Host ports are mapped to container ports to allow access from outside the host.
- Example: docker run -p 8080:80 nginx maps host port 8080 to container port 80.
- Port mapping uses NAT and iptables to forward traffic.


#### 🛠️ **Docker Networking Troubleshooting: Real-World Tips**


##### ✅ 1. **Check the Container's IP and Network**

```bash
docker inspect <container-name or ID>
```

Look for:

* `Networks` → see the container's assigned IP
* `NetworkMode`
* Which networks it's connected to

📌 Use `docker network inspect <network-name>` to see **all containers** attached to a specific network.


##### ✅ 2. **Ping Between Containers**

If two containers are on the **same custom bridge** network, they should resolve each other by name:

```bash
docker exec container1 ping container2
```

❗ On the **default `bridge` network**, DNS-based name resolution **does not work**. Use container IPs or use a user-defined bridge network.


##### ✅ 3. **Check if Port is Exposed and Published**

You might be running a service inside a container, but if it’s not mapped to a host port, it won’t be accessible:

```bash
docker ps
```

Check for `PORTS` column like `0.0.0.0:8080->80/tcp`.

📌 If missing, the container is not exposed to the host. Re-run with:

```bash
docker run -p 8080:80 <image>
```


##### ✅ 4. **Check Host’s Port Availability**

Ensure the **host port isn’t already in use**:

```bash
sudo lsof -i -P -n | grep LISTEN
```

If something else is using the host port, Docker won't bind to it.


##### ✅ 5. **Check iptables Rules**

Docker sets up iptables for NAT and forwarding.

To list Docker-related rules:

```bash
sudo iptables -t nat -L -n
```

📌 If you've disabled iptables or a firewall has blocked rules, containers may not get outbound access.


##### ✅ 6. **Check DNS Resolution in Containers**

Sometimes DNS isn’t resolving from inside the container:

```bash
docker exec <container> cat /etc/resolv.conf
```

Try:

```bash
docker exec <container> ping google.com
```

✅ Fix:

* Use Google DNS: `--dns 8.8.8.8` when running container
* Or configure DNS in `/etc/docker/daemon.json`

```json
{
  "dns": ["8.8.8.8", "1.1.1.1"]
}
```


##### ✅ 7. **Use `nsenter` or `ip netns` to Explore Namespaces**

For deep debugging:

```bash
PID=$(docker inspect -f '{{.State.Pid}}' <container>)
sudo nsenter -t $PID -n ip a
```

This lets you inspect the container's network from the **host perspective**.


##### ✅ 8. **Check for Duplicate Networks or Conflicting Subnets**

Sometimes your Docker networks clash with your **host network (e.g., VPN or LAN)**.

```bash
docker network inspect <network>
```

If your container’s subnet overlaps with host/VPN range, change it:

```bash
docker network create \
  --subnet=192.168.200.0/24 \
  my_custom_net
```


##### ✅ 9. **Testing Connectivity with Netcat or Curl**

Inside container, test connectivity:

```bash
# Try to reach an external service
curl http://host.docker.internal:8080

# Check if a port is open
nc -zv <target-host> <port>
```

`host.docker.internal` works on **Docker Desktop (Mac/Windows)** for host access.


##### ✅ 10. **Container Has Internet Issues? Check IP Forwarding**

Verify IP forwarding is enabled on host:

```bash
cat /proc/sys/net/ipv4/ip_forward
```

It should return `1`. If not:

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```


##### Bonus Tip: 🧰 Use BusyBox or Alpine for Quick Debug

Start a lightweight container for testing:

```bash
docker run --rm -it --network=my_net alpine sh
# Inside: ping, nslookup, curl, etc.
```

Install tools if needed:

```sh
apk add curl iputils iproute2 net-tools
```


#### ✅ TL;DR – Quick Checklist:

| What to Check               | Tool/Command                              |
| --------------------------- | ----------------------------------------- |
| Network and IP config       | `docker inspect`                          |
| Container-to-container ping | `docker exec ping <other-container>`      |
| Port bindings               | `docker ps`, `-p` option                  |
| DNS issues                  | `cat /etc/resolv.conf`, `ping google.com` |
| Iptables/NAT issues         | `sudo iptables -t nat -L -n`              |
| Namespace debugging         | `nsenter -t <PID> -n`                     |
| Host port conflicts         | `lsof -i`, `netstat`, `ss`                |


---

end!!
