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

### Dockerfile instructions

#### ENTRYPOINT

```bash
ENTRYPOINT ["/usr/sbin/nginx", "-g", "daemon off;"]
```

- Specify the command to run when `docker run` is invoked
- Any args passed to `docker run` will be appended to the entrypoint
- Instruction will be prepended by `bin/sh -c` so array syntax is preferred

#### CMD

```bash
CMD ["/bin/bash", "-l"]
```

- Specify the default command to run when `docker run` is invoked
- Will be overwritten by args passed to `docker run`
- Instruction will be prepended by `bin/sh -c` so array syntax is preferred

##### ENTRYPOINT and CMD combination

For base images

See [this example](examples/entrypoint-cmd/Dockerfile)

#### WORKDIR

```bash
WORKDIR /opt/webapp
```

- Change the directory in build phase
- Can be used several times within the Dockerfile
- Can be overwritten in runtime with `docker run -w /alternative/workdir ...`

#### ENV

```bash

# Just one environment variable
ENV TERRAFORM_VERSION 0.12.24

# Several at once
ENV SIMPLE=yes ONE_WITH_SPACES="I have spaces"

# Then, they can be user
CMD echo $TERRAFORM_VERSION
```

- To set environment variables for later Dockerfile instructions and runtime
- Subsequent `RUN` instructions will be prepended by `TERRAFORM_VERSION=0.12.24` assignment
- Environment variables can be passed or overwritten in runtime with `docker run -e "WEB_PORT=8080" ...`

#### USER

```bash
# Specifying the user
USER tuki

# Specifying user and group (to avoid ambiguity)
USER tuki:awesomepeople

# Specifying user and group with IDs
USER 1000:1001
```

- Specified the user that will run the image
- User and/or group can be specified by its ID instead of the name
- Defaults to `root`

#### VOLUME

```bash
VOLUME ["/opt/project"]
```

- Volumes map a path inside the container with a path in the host

//TODOOOOOOOOOOOOOOOOOOOOOOOOOOO!!!

#### ADD

```bash
# Original files or folders should be inside the build context
ADD someFile.o bin/

# Jar file will be downloaded into bucket folder
ADD https://dl.bintray.com/ptrthomas/karate/karate-0.9.2.jar bucket/

# Tarball will be automatically unpacked with -x option, i.e, no overwrites & result will be the union
# of what was in destination before and extracted files from tarball
ADD latest.tar.gz /var/www/
```

- For copying files inside the docker container in build phase
- Has also enhanced capabilities to download files from websites and unpack tarballs

#### COPY

```bash
# Original files or folders should be inside the build context
COPY someDir/ bin/
```

- For copying files inside the docker container in build phase
- Focused on local files, no enhancements like `ADD`
- If origin ends with `/` it is considered a directory and will be copied with all its contents inside destination path
- If destination path doesn't exist, `mkdir -p` will be executed to create it

#### LABEL

```bash
# One label at a time
LABEL version="0.0.1"

# Several labels at once to avoid several layers creation
LABEL location="Ciudad de los Ángeles" type="Data Center" role="database"
```

- Labels store metadata about the image and therefore, the container
- Labels of an image or container can be checked with `docker inspect container-or-image-name`

#### STOPSIGNAL

```bash

```

- 
