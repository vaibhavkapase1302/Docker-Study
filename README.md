# Top Docker Commands

```
docker --version
```

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

## Docker Volume


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
