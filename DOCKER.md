---
tags:
	- Docker
	- Syntax
---

# Docker

## Helpful debugging tips

### Container crashes

If the container is crashing then `docker logs` may not contain anything useful. What is the error code in `docker ps -a`?

The next best thing to do is override the default entrypoint with a terminal and try the command yourself:
```
> docker run -p 183:1883 -it --entrypoint /bin/bash 6ac7bf7b673a
root@043058f0fd8d:/# /usr/sbin/mosquitto
```


## Logs

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