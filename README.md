# docker-cheatsheet

## Docker System Commands

### About docker installation

```bash
docker info
```

## Container Management

### Run a container in the foreground attaching an interactive shell to it

```bash
docker run --name tiny_linux -i -t alpine /bin/sh
```

### Run a daemonized container in the background

```bash
docker run --name greeting_daemon -d alpine /bin/sh -c "while true; do echo hello world; sleep 1; done"
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
