---
tags:
	- Docker
	- Syntax
---

# Docker

## Helpful debugging tips

### `Command tips`

In the beginning days of docker containers could be managed with commands that look like this:

```
> docker <command>

> docker run 
> docker stop
> docker rm
> ...
```

Now since there are so many new docker commands, it is preferred to use the management-style commands for readability. The old way is still supported.

```
> docker <entity> <command>

> docker container logs
> docker container exec
> docker network ls
> docker image rm
> ...
```



## Container tips

If the container is crashing then `docker logs` may not contain anything useful. What is the error code in `docker ps -a`?

The next best thing to do is override the default entrypoint with a terminal and try the command yourself:
```
> docker run -p 183:1883 -it --entrypoint /bin/bash 6ac7bf7b673a
root@043058f0fd8d:/# /usr/sbin/mosquitto
```

### Running an application in a container

If you are starting an applicaiton in a container at runtime (for example, launching `sshd` when the container runs ), and that application reports an error when the container is started, first make sure you are running the application with the correct arguments. Then, if you are still seeing an error, make sure the dockerfile line endings are `LF` terminated and not `CRLF` terminated.

### Starting an interative shell in a running container

You can always start an interactive shell in a running container by executing the following command. Make sure to use the container Id and not the image Id.
```
> docker exec -it 6ac7bf7b673a /bin/bash
```


### Getting logs

The `docker logs` command shows information logged by a running container.

```
> docker logs 21da4564aeae
1578336525: mosquitto version 1.4.15 (build date Tue, 18 Jun 2019 11:42:22 -0300) starting
1578336525: Using default config.
1578336525: Opening ipv4 listen socket on port 1883.
1578336525: Opening ipv6 listen socket on port 1883.
```

### Logging drivers

Docker includes multiple logging mechanisms to help get information from running containers and services. Each docker daemon has a default logging driver which each container uses unless you configure it to use a different logging driver. **Warning: if you use a different logging driver, `docker logs` may not show useful information**.

## Storage tips

By default, all files created inside a container are stored on a writable container layer. Data does not persist when the container no longer exists. You can't easily move data elsewhere. 

There are two options for persistent file storage for containers: `volumes` and `bind mounts`.  No matter which way you choose, it looks the same in the container (either a directory or individual file in the container's filesystem).

### volumes

Volumes are stored in a part of the host filesystem which is managed by docker `/var/lib/docker/volumes`. **Non-docker processes should not modify this part of the filesystem**.

Volumes are created by docker with `docker volume create`, which creates a directory on the host filesystem. When you mount the volume into a container, this directory is what is mounted into the container. You can remove unused volumes with `docker volume prune`

Good for sharing data among running containers, good for storing data on a remote host or cloud provider rather than locally, help to decouple the configuration of the docker host from the container runtime. You can always stop the containers using the volume and back up the volume's directory if you need to migrate docker hosts.

### bind mounts

Are stored anywhere on the host filesystem. Non-docker processes can modify them at any time. 

Available since the early days of docker but have limited functionality compared to volumes. You can't use docker commands to directly manage bind mounts.

Good for sharing configuration files from the host machine to the container, sharing source code or build artifacts, good when the directory structure of the docker host is guaranteed to be consistent with the bind mounts the containers require.

### Free up space

Docker takes a conservative approach to cleaning up unused objects (*nothing is done unless you tell it to*). For each type of object, docker provides a prune command and additional filter expressions. `docker system prune` lets you clean up multiple objects.

`docker image prune`

Cleans up dangling images: those that are not tagged and not referenced by any container. To remove all images which are not used by existing containers use the `-a` flag.

`docker container prune`

Containers are not automatically removed unless you started it with the `--rm` flag. B y default, all stopped containers are removed.

`docker volume prune`

Volumes are never removed automatically because that could destroy data. By default, all unused volumes are removed. 

`docker network prune`

Networks don't take up much spaced but they do create iptables rules, bridge network devices, routing table entries, etc. By default, all unused networks are removed. 

`docker system prune --volumes`

Clean up everything.