# docker-cheatsheet

## Docker system commands

```bash
# About docker installation, also provides images & containers counters
docker info
```

## Container management

```bash
## Run a container in the foreground attaching an interactive shell to it
docker run --name mini_linux -i -t alpine /bin/sh
```

```bash
## Run a daemonized container in the background
docker run --name greeting_daemon -d alpine /bin/sh -c "while true; do echo hello world; sleep 1; done"
```

```bash
# Start an stopped container
docker start mini_linux
```

```bash
# Reopen the interactive session in a running container
docker attach greeting_daemon
```

```bash
# Show logs of a running container with timestamps
docker logs -tf greeting_daemon
```

```bash
# Inspect container processes
docker top greeting_daemon
```

```bash
# Running a process inside an already running container
docker exec -d greeting_daemon echo "hello" >> myFile
```

```bash
# Stop a running container
docker stop greeting_daemon
```
