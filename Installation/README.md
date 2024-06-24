Using docker.io to install Docker on Ubuntu, which is a simplified process provided by the Ubuntu package repository. 

**Update the package database:**

```sh
sudo apt-get update
```

**Install Docker:**

```sh
sudo apt-get install docker.io
```

**Start the Docker service:**

```sh
sudo systemctl start docker
```

**Enable Docker to start at boot:**

```sh
sudo systemctl enable docker
```

**Check Docker version to verify installation:**

```sh
docker --version
```

**Manage Docker as a non-root user (optional but recommended):**

**Create the Docker group if it does not exist:**

```sh
sudo groupadd docker
```

**Check USER:**

```sh
echo $USER
```

**Add your user to the Docker group:**

```sh
sudo usermod -aG docker $USER
```



