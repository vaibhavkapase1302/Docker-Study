## React Frontend

```Dockerfile
# ==== CONFIGURE =====
# Use a Node 18 base image
FROM node:18-alpine as builder

ARG PACKAGE_READ_TOKEN
# Set the working directory to /app inside the container
WORKDIR /app
# Copy app files
COPY . .
# ==== BUILD =====
# Install dependencies (npm ci makes sure the exact versions in the lockfile gets installed)
RUN echo "//npm.pkg.github.com/:_authToken=${PACKAGE_READ_TOKEN}" > ~/.npmrc \
    && npm install --force && npm cache clean --force \
    && rm ~/.npmrc

ENV GENERATE SOURCEMAP false
# Build the app
RUN INLINE_RUNTIME_CHUNK=false npm run build

RUN rm -rf ./node_modules
RUN rm -rf node_modules

FROM node:18-alpine as runner

WORKDIR /app

COPY --from=builder /app .

# ==== RUN =======
# Set the env to "production"
ENV NODE_ENV production
# Expose the port on which the app will be running (3000 is the default that `serve` uses)
EXPOSE 3000
# Not needed to expose (not best practice for security reasons)

RUN chmod +x env.sh

# Start the app
CMD ["npm",  "run",  "serve-build" ]
```

## Node Backend

```Dockerfile
#use builder to builder and then copy to runner, it removes 800mb of npm cache

FROM node:18-alpine AS builder

ARG PACKAGE_READ_TOKEN

RUN mkdir -p /usr/src/facctum-internal-ops-service && chown -R node:node /usr/src/facctum-internal-ops-service

WORKDIR /usr/src/facctum-internal-ops-service

COPY --chown=node:node . .

USER node

# Install npm packages from private GitHub repository
RUN echo "//npm.pkg.github.com/:_authToken=${PACKAGE_READ_TOKEN}" > ~/.npmrc \
    && npm install --force && npm cache clean --force \
    && rm ~/.npmrc

RUN npm cache verify

RUN npm run build

RUN rm -rf ./node_modules

RUN rm -rf node_modules


FROM node:18-alpine AS runner

RUN apk add --no-cache openssh 

#RUN mkdir -p /usr/src/facctum-internal-ops-service-run && chown -R node:node /usr/src/facctum-internal-ops-service-run

WORKDIR /usr/src/facctum-internal-ops-service-run

COPY --chown=node:node --from=builder /usr/src/facctum-internal-ops-service/dist/bundle .

# EXPOSE 3000

# Don't need node command in distroless
CMD ["node", "--expose-gc", "index.mjs"]
```

```dist/bundle``` 
In web development, "dist" usually refers to the directory where a project's compiled and bundled files are placed, while "bundle" refers to the process of combining multiple source files (like JavaScript or CSS) into a single file for efficient loading and deployment. 


```index.mjs```
It's a single file in node than hold all runnable files like index.html
(Index merge js) for rem

## Python

```Dockerfile
FROM python:3.12.7-slim AS builder

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    gcc \
    libgl1-mesa-dev \
    mkdocs \
    && rm -rf /var/lib/apt/lists/*

ARG wd=/var/task/
COPY . ${wd}

RUN pip3 install --upgrade pip && pip3 install -r ${wd}requirements.txt

CMD ["python3", "/var/task/index.py"]
```
