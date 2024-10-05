### Problem Statement: 01 Docker PostgreSQL Port Conflict Resolution

Here's a template you can use for your `README.md` file on GitHub, documenting the problem and the solution:

#### Problem Statement

While running two instances of PostgreSQL on Docker, both instances attempted to bind to the same default port (`5432`). This resulted in a port conflict, preventing one of the containers from starting successfully.

### Solution

To resolve the port conflict, we utilized Docker Compose to manage the container configurations and assigned different port numbers to each PostgreSQL container. This allowed both containers to run simultaneously without any conflicts.

### Steps to Reproduce the Problem

1. **Dockerfile Setup:**
    - We created a `Dockerfile` based on the official PostgreSQL image.
    - The image was built using the `docker build` command.

    ```dockerfile
    # Dockerfile
    FROM postgres:latest

    ENV POSTGRES_USER=rootß
    ENV POSTGRES_PASSWORD=mysecretpassword

    EXPOSE 5432
    ```

    - Build the image:

    ```bash
    docker build -t my_custom_postgres .
    ```

2. **Running the Containers:**
    - Initially, we tried to run two PostgreSQL containers using the following commands:

    ```bash
    docker run --name <container_name> -e POSTGRES_PASSWORD=<password> -e POSTGRES_USER=<root> -p 5432:5432 -d postgres   
    ```
    
    ```bash
    docker run --name postgres_instance_1 -e POSTGRES_PASSWORD=mysecretpassword -e POSTGRES_USER=root -p 5432:5432 -d my_custom_postgres
    ```

    ```bash
    docker run --name postgres_instance_2 -e POSTGRES_PASSWORD=anotherpassword -e POSTGRES_USER=admin -p 5432:5432 -d my_custom_postgres
    ```

    - This led to a conflict since both containers were trying to use port `5432`.

#### Resolution Using Docker Compose

Docker Compose was used to assign different external port numbers to each container, while the internal container ports remained the same (`5432`).

**Method 1. Create a `docker-compose.yml` File:**

    ```yaml
    version: '3.8'

    services:
      postgres_instance_1:
        image: my_custom_postgres
        environment:
          POSTGRES_PASSWORD: mysecretpassword
          POSTGRES_USER: root
        ports:
          - "5432:5432"

      postgres_instance_2:
        image: my_custom_postgres
        environment:
          POSTGRES_PASSWORD: anotherpassword
          POSTGRES_USER: admin
        ports:
          - "5433:5432"
    ```

    - In this configuration:
      - `postgres_instance_1` is exposed on port `5432`.
      - `postgres_instance_2` is exposed on port `5433`.

2. **Run the Containers Using Docker Compose:**

    ```bash
    docker-compose up -d
    ```

    - The `-d` flag runs the containers in detached mode.

3. **Verification:**

    - Both PostgreSQL instances should now be running without any port conflicts.
    - You can verify this by listing the running containers and checking the ports:

    ```bash
    docker ps
    ```

    - The output should indicate that one container is mapped to `localhost:5432` and the other to `localhost:5433`.
  
  **Method 2. Changing port no. at the time of docker run**

    docker run --name postgres_instance_1 -e POSTGRES_PASSWORD=mysecretpassword -e POSTGRES_USER=root -p 5432:5432 -d my_custom_postgres

    docker run --name postgres_instance_2 -e POSTGRES_PASSWORD=anotherpassword -e POSTGRES_USER=admin -p 5433:5432 -d my_custom_postgres

#### Conclusion

By using Docker Compose, we successfully resolved the port conflict issue and allowed both PostgreSQL containers to run simultaneously. This method can be extended to manage more complex Docker setups, ensuring that services run smoothly without port collisions.


Certainly! Here’s a complete documentation that includes the problem statement, solution findings, and a detailed explanation of the `docker-entrypoint.sh` script.

---

#### Problem Statement: 02 Docker Entry Point Script Permission Issue


**Description**:
During the deployment of a Node.js application using Docker, an error was encountered when running the container. The specific error message was:

```
/usr/local/bin/docker-entrypoint.sh: 11: exec: .: Permission denied
```

This indicated that the entry point script (`docker-entrypoint.sh`) was not executing due to permission issues. This problem hindered the container from starting and running the Node.js application as intended.

**Context**:
The Docker setup aimed to create a reliable environment for deploying the application. The entry point script was intended to handle initialization tasks and start the application. However, permission issues prevented the script from executing, resulting in application deployment failure.

---

#### Solution Findings

**1. Verification of Script Location**:
   - Confirmed that the `docker-entrypoint.sh` file was present in the correct directory where the Docker build context was defined.

**2. Setting Executable Permissions**:
   - Applied executable permissions to the `docker-entrypoint.sh` script using the command:
     ```bash
     chmod +x docker-entrypoint.sh
     ```

**3. Check Shebang Line**:
   - Verified that the first line of the entry point script correctly specified the interpreter:
     ```bash
     #!/bin/sh
     ```

**4. Dockerfile Adjustments**:
   - Ensured the Dockerfile correctly copied the entry point script and set permissions:
     ```dockerfile
     COPY docker-entrypoint.sh /usr/local/bin/
     RUN chmod +x /usr/local/bin/docker-entrypoint.sh
     ```

**5. Rebuilding the Docker Image**:
   - After making the necessary changes, rebuilt the Docker image using:
     ```bash
     docker build -t my-node-app:v1 .
     ```

**6. Successful Execution**:
   - Ran the Docker container successfully with the command:
     ```bash
     docker run -p 3002:3002 my-node-app:v1
     ```
   - The application started as expected, indicating that the issue was resolved.

---

#### Detailed Explanation of `docker-entrypoint.sh`

**Purpose**:
The `docker-entrypoint.sh` script serves as the entry point for the Docker container, performing initialization tasks before starting the main application. It allows for flexibility and control over the container's startup process.

#### Structure of `docker-entrypoint.sh`:

Here’s a typical example of the script:

```bash
#!/bin/sh
set -e

# Any initialization or setup commands can go here

# Start the main process
exec "$@"
```

#### Breakdown of the Script:

1. **Shebang Line (`#!/bin/sh`)**:
   - This line specifies that the script should be run using the Bourne shell. It is essential for telling the operating system which interpreter to use.

2. **`set -e`**:
   - This command makes the script exit immediately if any command fails (returns a non-zero exit status). This behavior is crucial for error handling, ensuring that the container does not run with an improper state if initialization fails.

3. **Initialization Commands**:
   - Any necessary setup tasks can be performed here. For example:
     ```bash
     echo "Setting up the application..."
     export NODE_ENV=production
     ```
   - This allows for the configuration of environment variables or other settings required by the application.

4. **`exec "$@"`**:
   - This line executes the command that was passed to the entry point script. The `"$@"` expands to all arguments provided, enabling the container to run the main application or any specified command.
   - Using `exec` replaces the shell process with the command, allowing for proper signal handling (e.g., SIGTERM for graceful shutdown). This is vital for maintaining the application's responsiveness and stability.

#### Running the Container

When you run your Docker container, you might use a command like:

```bash
docker run my-node-app:v1 node app.js
```

- Here, `node app.js` becomes the argument passed to `docker-entrypoint.sh`, allowing the entry point script to handle its execution.

---

#### Conclusion

The permission issue with the `docker-entrypoint.sh` script was identified and resolved through a systematic approach involving permission checks, script validation, and Dockerfile adjustments. This ensured that the Node.js application could be deployed successfully in a Docker container, allowing for a smooth development and deployment process.

---

#### Recommendations

- **Future Best Practices**:
  - Always ensure that any script copied into a Docker image has the necessary executable permissions.
  - Use `set -e` in entry point scripts to handle errors gracefully and prevent unexpected behavior.
  - Document any initialization steps within the entry point script to clarify its purpose and function.

This comprehensive documentation should provide clarity on the problem, the steps taken to resolve it, and a thorough understanding of the `docker-entrypoint.sh` script's role in your Docker setup. Feel free to modify any sections as per your specific needs!
