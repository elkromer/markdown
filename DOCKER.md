---
tags:
	- Docker
	- Compose
	- Advanced
---

# Working with Docker
> Docker is a set of platform-as-a-service products that use OS-level virtualization to deliver software in packages called containers.  A container is a standardized unit of software.

Docker is an open platform for developing, shipping, and running applications. It provides the ability to to package and run an application in an isolated environment. It enables you to separate your applications from your infrastructure so you can deliver software quickly.

There are two types of containers: Windows and Linux containers. Both are still actively developed and evolving rapidly. 

## Docker Engine

A client-server application with three major components. 
* A server, called `Docker daemon` or `dockerd`.
* A REST API which programs can use to talk to the daemon to instruct it what to do
* A command-line interface client (the `docker` command).

![Docker Engine](/resources/docker-daemon-visualization.png)

### Docker Objects Overview

* `Images`: A template with instructions for creating a container. The binaries, libraries, and source code that make up the application.
* `Containers`: A runnable instance of an image.
* `Services`: Allow you to scale containers across multiple Docker daemons which all work together. A service allows you to define the desired state, such as the number of replicas of the service that must be available at any given time.

## Rundown

This rundown assumes you have written a dockerfile that builds an image successfully.

### A quick note on Docker commands

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

### What's in an Image

The image contains the app binaries and dependencies as well as metadata about how to run the image. **It's not booting up a full OS.** That is one of the defining differences between a running image and a virutal machine. It is really just starting an application. 

There is no kernal and there are no kernal drivers. The kernal is actually provided by the host OS. The image can be as small as a single file or as large as multiple gigabytes.

Images are designed using the union filesystem concept of "making layers about the changes". All images in the beginning start from a blank layer known as `SCRATCH`. Every set of changes that happens after that on the filesystem in the image is another layer. The output of the below command shows the layers contained within the image from the bottom-up.

```
docker image history nginx
```
To create an image, you can use `docker build ...` which is equivalent to 

```
docker image build . -f Dockerfile
```

### Working with Containers

From the perspective of a running container, the underlying image it was started from is read-only. Any changes to a container are written to the writable container layer and will persist across stopping and starting. Once the container is destroyed, the data is destroyed. 

How do you get into the container once its started? SSH servers are not necessary. 

**Option 1** is to call `docker run` and specify the `--tty` and `--interactive` options and specify an alternate command. The specified command will overwrite the `CMD` specified in the dockerfile.

```
docker container run -it --name test nginx bash
```
*Note: once you type `exit` the container stops because `CMD` was overwritten with `bash`*

**Option 2** is to call `docker start` on a stopped container and specify the `--attach` and `--interactive` options.

```
docker container start nginx -ai
```

**Option 3** is used when you want to create a shell inside a container that is already running a program.

```
docker container exec -it nginx bash
```

### Persistent Data

Containers are meant to be temporary and disposable. What about the unique data your app produces? Ideally the applicaiton shouldn't produce data alongside the application binaries. **Separation of concerns**. You can use Volumes or Bind Mounts to keep the unique data elsewhere and redeploy changes to the container while keeping the data intact.

* `Volumes` are a configuration option for a container that creates a special location outside of the container union filesystem to store unique data. Preserves the data across removals and allows attachment to any container you want.
* `Bind Mounts` are a mapping of a host directory or file into a container.

To tell a container it should use a **volume** you have three options.

**Option 1** is to add a `VOLUME` command to the dockerfile, which tells docker when we start a container from this image to actually create a new volume location and assign it to this directory in the container. Any files you put in that location will outlive the container until the volume is deleted.

```
VOLUME /var/lib/mysql
```
*This creates a new unnamed volume every time a container is started.*

**Option 2** is to specify a named volume in the `run` command. It allows you to specify a new volume, use an existing volume,

```
docker container run -d --name mysql -e MYSWL_ALLOW_EMPTY_PASSWORD=true -v mysql-db:/var/lib/mysql mysql
```

**Option 3** is to create a volume with the `docker volume create` command and attach it to the container later. This is really only used when you want to create a volume with a specific driver.

To tell a container it should use a **bind mount** you must do it like this depending on the host:

**Mac/Linux**:
```
docker container run ... -v /Users/reese/stuff:/path/container
```
**Windows**
```
docker container run ... -v //c/Users/reese/stuff:/path/container
```

Basically just two locations pointing to the same file(s). Skips the union filesystem and host files overwrite any in the container. In the docker run command, you can use the keywords `$(pwd)` on linux or `${pwd}` on Windows on the left side of the `--volume` option

### Working with Networks

You can't assume from minute to minute the ip addresses of the containers on Docker's private network to stay the same as containers are created and destroted. That is why automatic DNS name resolution is a big deal. Virtual networks can be created like this:

```
docker network create my_app_net
```

**Note that automatic DNS name resolution is not available on the default bridge network without additional configuration.** That is why it is usually recommended to create your own network rather than use the default network, especially when deploying multiple containers that need to talk to each other.


You can specify a network when creating a container:

```
docker container run -d --name nginx --network my_app_net nginx
```

Containers on the same network can find each other based on their hostnames (container names). Ping example:

```
> docker container exec -it openssh76 ping nginx
PING nginx (172.18.0.2) 56(84) bytes of data.
64 bytes from nginx.my_app_net (172.18.0.2): icmp_seq=1 ttl=64 time=0.673 ms
64 bytes from nginx.my_app_net (172.18.0.2): icmp_seq=2 ttl=64 time=0.086 ms
64 bytes from nginx.my_app_net (172.18.0.2): icmp_seq=3 ttl=64 time=0.061 ms
64 bytes from nginx.my_app_net (172.18.0.2): icmp_seq=4 ttl=64 time=0.064 ms
^C

> docker container exec -it nginx ping openssh76
PING openssh76 (172.18.0.3) 56(84) bytes of data.
64 bytes from openssh76.my_app_net (172.18.0.3): icmp_seq=1 ttl=64 time=0.069 ms
64 bytes from openssh76.my_app_net (172.18.0.3): icmp_seq=2 ttl=64 time=0.062 ms
64 bytes from openssh76.my_app_net (172.18.0.3): icmp_seq=3 ttl=64 time=0.119 ms
^C
```

Calling `docker network connect` effectively plugs in a NIC to the specified container.

## Docker Compose

Configure relationships between containers! Why? Create one-liner development environment startups. Docker compose consists of two things:

1. YAML-formatted file that describes our solution options `docker-compose.yml`
2. A CLI tool `docker-compose` used for local dev/test automation

Good for replacing scripts that call Docker `run`. With the beginning of v1.13 they can be used with `docker` directly with Swarm. 

A template for `docker-compose`:

```yml
version: '3.1'  # if no version is specificed then v1 is assumed. Recommend v2 minimum

services:  # containers. same as docker run
  servicename: # a friendly name. this is also DNS name inside network
    image: # Optional if you use build:
    command: # Optional, replace the default CMD specified by the image
    environment: # Optional, same as -e in docker run
    volumes: # Optional, same as -v in docker run
  servicename2:

volumes: # Optional, same as docker volume create

networks: # Optional, same as docker network create
```

Good for defining a multi-container service. Takes all the work out of creating `docker run` commands and simplifies your container startup scripts.

You can even specify persistent data storage in a compose file. Many images on docker already have built-in support for databases by setting the appropriate configuration in environment variables. The below file is a good example:
```yml
version: '2'

services:

  wordpress:
    image: wordpress
    ports:
      - 8080:80
    environment:
      WORDPRESS_DB_HOST: mysql
      WORDPRESS_DB_NAME: wordpress
      WORDPRESS_DB_USER: example
      WORDPRESS_DB_PASSWORD: examplePW
    volumes:
      - ./wordpress-data:/var/www/html # bindmount for live edits

  mysql:
    image: mariadb
    environment:
      MYSQL_ROOT_PASSWORD: examplerootPW
      MYSQL_DATABASE: wordpress
      MYSQL_USER: example
      MYSQL_PASSWORD: examplePW
    volumes:
      - mysql-data:/var/lib/mysql #volname:/path/on/container

volumes:
  mysql-data: # definition
```
The `mysql` container stores the data we want to preserve in its `/var/lib/mysql` directory so we just specify **the name of a volume** and **a volume definition** to tell compose to create a volume for us.

### Image Building in a Compose File

Another thing compose can do for you is build the images at runtime. It can optionally rebuild them every time but it uses the image cache by default. 

```docker
services:
  proxy:
    build:
      context: .
      dockerfile: nginx.Dockerfile
    image: nginx-custom
    ports:
      - '80:80'
```

In the above file, docker-compose will first look for an image called `nginx-custom` in the image cache and if it doesn't find it, it will use the `build:` options to create an image and name it `nginx-custom`.



### docker-compose CLI

Compose comes with Docker for Windows but is a separate download for Linux. The two main commands that do everything are:
* `docker compose up` sets up volumes/networks and starts all containers
* `docker compose down` stops all containers and removes containers/volumes/networks. 

Volumes are not removed by default. You must specify the `--volume` option in `docker-compose down` to remove a volume. A lot of the commands you use in docker are available in docker-compose. For example, `-d` for detach, `logs` for logs, `--help`, etc.

### Developer onboarding just got easier

If all your projects had a Dockerfile and a docker-compose.yml then "new developer onboarding" would be as simple as:

```
git clone github.com/some/repo
docker-compose up
```

