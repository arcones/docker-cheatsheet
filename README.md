# docker-cheatsheet

## About docker installation

```bash
docker info
```

It will report, among other things:

- Info about how many containers are running, paused, stopped
- How many images are in the system
- The docker engine version and license
- The logging, cgroups drivers in use
- From where docker is running
- Information about the host machine

## Run a container

```bash
docker run -i -t ubuntu /bin/bash
```

In this command:

- `-i` flag keeps STDIN open for the container
- `-t` flag assign a pseudo-tty to the container
- The combination `-i -t` or `-it` provides an interactive shell in the new container
