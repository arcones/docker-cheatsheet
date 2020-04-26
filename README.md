# docker-cheatsheet

## Docker System Commands

### About docker installation

```bash
docker info
```

## Container Management

### Run a container in the foreground attaching an interactive shell to it

```bash
docker run --name tiny_linux -i -t alpine:3.11.6 /bin/sh
```

### Run a daemonized container in the background

```bash
## As no tag is provided, it will fallback to "latest"
docker run --name greeting_daemon -d alpine /bin/sh -c "while true; do echo hello world; sleep 1; done"
```

### List running containers

```bash
docker ps
```

### List all containers

```bash
docker ps -a
```

### Start an stopped container

```bash
docker start tiny_linux
```

### Reopen the interactive session in a running container

```bash
docker attach greeting_daemon
```

### Show logs of a running container with timestamps

```bash
docker logs -tf greeting_daemon
```

### Inspect container

```bash
docker inspect greeting_daemon
```

### Inspect containers applying filters on the output shown

```bash
docker inspect greeting_daemon greeting_daemon_2 --format='{{ .NetworkSettings.IPAddress }}'
```

### Inspect container processes

```bash
docker top greeting_daemon
```

### Running a process inside an already running container

```bash
docker exec -d greeting_daemon echo "hello" >> myFile
```

### Stop a running container

```bash
docker stop greeting_daemon
```

### Schedule a daemonized container that, if stopped for any reason, will be restarted

```bash
docker run --restart=always --name greeting_daemon -d alpine /bin/sh -c "while true; do echo hello world; sleep 1; done"
```

### Schedule a daemonized container that, if stopped with a non zero code, will be restarted up to five times

```bash
docker run --restart=on-failure:5 --name greeting_daemon -d alpine /bin/sh -c "while true; do echo hello world; sleep 1; done"
```

### Delete a container

```bash
docker rm greeting_daemon
```

### Delete all containers

```bash
docker rm -f $(docker ps -a -q)
```

## Working with images

### List available images in the host

```bash
docker images
```

### List only some specific images available in the host

```bash
docker images alpine
```

### Pull an image, unambiguously identified by its tag, from the registry

```bash
docker pull alpine:3.11.6
```

### Search for images in the registry

```bash
docker search nginx
```

### Create images by committing modified containers (not recommended)

```bash
docker run -it alpine /bin/sh

## Then, inside the container do the changes, for example...
apk -v --update add python

docker commit -m "Alpine with python installed" -a "Marta Arcones" d3f78b0c3430 arcones/alpine-with-python
```

### Create images from Dockerfiles

```bash
cd examples/nginx

docker build -t="arcones/static_web:0.0.1" .

## The exposed port in the dockerfile must be published with the -p flag
docker run -d -p 80 --name static_web arcones/static_web:0.0.1 nginx -g "daemon off;"

## Equivalent command that will expose all the mentioned ports in the Dockerfile
docker run -d -P --name static_web arcones/static_web:0.0.1 nginx -g "daemon off;"

## Check the host address & port that has been mapped with container's 80 port
docker port static_web 80

## Check that the server is working
curl $(docker port static_web 80)
```

### Avoid cache during the build

```bash
docker build --no-cache -t="arcones/static_web:0.0.1" .
```

### Build from a repository

```bash
docker build -t="arcones/static_web:0.0.1" github.com/arcones/docker-cheatsheet/examples/nginx
```

### Trick to force recreation of some layers

See [this example](examples/cache-trick/Dockerfile)

### Inspect steps for image creation

```bash
docker history arcones/alpine-with-python
```

### Port binding

```bash
## Bind 8080 host's port to container port 80
docker run -d -p 8080:80 --name static_web arcones/static_web:0.0.1 nginx -g "daemon off;"
```

### Interface binding

```bash
docker run -d -p 127.0.0.1:8080:80 --name static_web arcones/static_web:0.0.1 nginx -g "daemon off;"
```
