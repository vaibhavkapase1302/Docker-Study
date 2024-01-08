# Docker-Study
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
