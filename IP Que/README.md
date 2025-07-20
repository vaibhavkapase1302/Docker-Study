# 🐳 Docker:

1. What is the difference between Virtual Machines and Containers?
2. Explain the Docker lifecycle.
3. Write some Docker commands. (I don’t remember the exact commands that were asked.)
4. Write a Dockerfile for one application. Explain each layer in it (any tech stack).
5. What is a docker-compose file? Explain what it does. Write one sample file if you can.
6. By default, which Docker network is present?
7. What is the purpose of a multi-stage Dockerfile? How does it reduce the image size?
8. Write the multi-stage Dockerfile for the same.
9. How does container-to-container communication happen? Explain it.
10. Mention some Docker network types and explain their real-world use cases.
11. What is the difference between CMD and ENTRYPOINT?
12. Where are Docker volumes stored?
13. What is the difference between COPY and ADD?
14. How many containers can we run in Docker exactly?
15. What happens to the data inside a container when you delete the running container?
16. What is the structure of your Dockerfile? How do you write one?
17. What is WORKDIR in a Dockerfile? What does it do?
18. How can you isolate networking between containers?
19. What steps would you take to secure your containers?What is a multi-stage build and why is it used?
20. How do you create a Docker network?
> A:Docker networks can be created using the docker network create command, followed by a name for the network and optional configuration parameters. For example, docker      network create mynetwork.

21. What Happens When a Running Docker Container is Stopped?

> When a Docker container is stopped, Docker first sends a `SIGTERM` signal to the container’s main process (PID 1) to allow **graceful shutdown and cleanup**; if the process doesn’t stop within the default _10-second_ grace period, Docker sends a `SIGKILL` signal to forcefully terminate it — transitioning the container state from **Running** to **Stopped**; this is done using the command `docker stop <container-id>`.

22. How does Docker ensure application isolation?

> Docker ensures application isolation by leveraging key Linux kernel features:
**Namespaces** _isolate_ system resources like processes (PID), network interfaces, and file systems, giving each container its own environment.
**Control Groups (cgroups)** manage and limit resource usage such as CPU, memory, and disk I/O, preventing any one container from monopolizing system resources.
**Union File Systems** (e.g., AUFS, OverlayFS) allow efficient, layered image creation with support for versioning and reuse, keeping containers lightweight.
**The container runtime** (e.g., containerd) manages the container lifecycle and enforces isolation at the OS level.
Together, these components ensure each container runs securely and independently on the same host.

23. Explain the lifecycle of a Docker container

📈 Lifecycle Flowchart 


```
          [Docker Image]
                 ↓
         docker create / run
                 ↓
            [Created]
                 ↓
         docker start / run
                 ↓
            [Running]
               ↓   ↓   ↓
       docker stop  docker kill  docker restart
            ↓         ↓            ↺
         [Stopped]  [Stopped]      ↓
               ↓        ↓        [Running]
         docker rm   docker rm      ↓
               ↓        ↓        ...
           [Removed] [Removed]
```

---

## Q&A

#### 1. ❓ How does Docker handle persistent data storage, and what are the differences between volumes, bind mounts, and tmpfs mounts?

**Answer:**
Docker provides several ways to persist data beyond container lifecycles:

* **Volumes:** Managed by Docker, stored in Docker’s storage area, portable and can be shared between containers; good for production data.
* **Bind mounts:** Directly mount a host directory or file into a container, providing high flexibility but less isolation and portability.
* **Tmpfs mounts:** Store data in the host’s memory (RAM), data is ephemeral and lost on container stop; useful for sensitive or temporary data.

Volumes are recommended for most persistent storage needs due to their portability and Docker management.

#### 2. ❓ What is the difference between `CMD` and `ENTRYPOINT` in a Dockerfile, and when would you use each?

**Answer:**

* **CMD:** Specifies default arguments for the container’s executable; can be overridden when running the container.
* **ENTRYPOINT:** Defines the executable that always runs when the container starts; arguments passed during `docker run` append to ENTRYPOINT.

Use `ENTRYPOINT` when you want to define the main command (like a fixed binary), and `CMD` to provide default arguments that can be overridden.

#### 3. ❓ Explain the concept of multi-stage builds in Docker and why they are beneficial.

**Answer:**
Multi-stage builds use multiple `FROM` statements in a Dockerfile to create intermediate images for building and testing, then copy only the necessary artifacts to the final, minimal image. This reduces image size and surface area by excluding build dependencies from the final image, improving security and efficiency.

#### 4. ❓ How does Docker networking work, and what are the primary network drivers?

**Answer:**
Docker networking isolates containers and provides communication methods between them and the host:

* **Bridge (default):** Containers on the same host can communicate via a virtual bridge.
* **Host:** Containers share the host’s network stack directly.
* **Overlay:** Enables multi-host networking by creating a virtual network across multiple Docker hosts (used with Swarm).
* **Macvlan:** Assigns MAC addresses to containers, making them appear as physical devices on the network.
* **None:** Disables networking for a container.

Each driver provides different levels of isolation, performance, and use cases.

#### 5. ❓ What happens internally when you run `docker run`?

**Answer:**
When `docker run` is executed, Docker performs:

* Image check/pull if not present locally.
* Creates a writable container layer on top of the image layers.
* Allocates namespaces and cgroups for isolation and resource control.
* Sets up networking according to the default or specified driver.
* Executes the container’s main process with specified `ENTRYPOINT` and `CMD`.
* Attaches STDIN/OUT if interactive.

---

## Docker Compose:- 

---

## Docker volumes:-

---

## Docker network:-

---

## Scenarios:

-> You’re running an app using docker-compose with low traffic. As traffic grows, how do you scale the application in AWS? What services will you choose — EKS, ECS or EC2? Why?

-> Application works via localhost but not over the web — how will you troubleshoot?

---
