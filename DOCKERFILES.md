---
tags:
	- Docker
	- Syntax
	- Dockerfile
---

# Dockerfiles

Docker can build images automatically by reading the instructions in a Dockerfile. The instructions defined in the dockerfile are all the commands a user could call on the commandline to assemble an image. Using `docker build` users can create an automated build that executes several command-line instructions in succession.

The `docker build` command builds an image from a Dockerfile and a context. The build's context is the set of files at a specified location PATH or URL. The path includes any subdirectories while the URL includes the repository and its submodules. 

**The file must begin with a `FROM` instruction.** This may be after [parser directives](https://docs.docker.com/engine/reference/builder/#parser-directives), [comments](https://docs.docker.com/engine/reference/builder/#format) and globally scoped [args](https://docs.docker.com/engine/reference/builder/#arg). The `FROM` instruction specifies the parent image from which you are building. 

It is generally best practice to run only one "thing" in a container. 

## RUN

The `RUN` instruction will execute any commands in a new layer on top of the current image and commit the results. The resulting image will be used in the next step of the dockerfile. 

Layering `RUN` instructions and generating commits conforms to the core concepts of Docker where commits are cheap and containers can be created from any point in a image's history, much like source control.

The `EXEC` command can be used as an alternative to avoid shell string munging and run commands that do not contain the specified shell executable.

## CMD

There can be only one `CMD` command in a dockerfile. **The main purpose of `CMD` is to provide defaults for an executing container**.  The defaults can include an executable or they can omit the executable, in which case you must specify an `ENTRYPOINT` instruction as well. The best use is to set the image's main command, allowing that image to be run as though it was that command (and use `CMD` as the default flags). **Its main purpose is to allow you to configure a container that will run as an executable**

```docker
CMD ["executable","param1","param2"] (exec form, this is preferred)
CMD command param1 param2 (shell form)
CMD ["param1","param2"] (as default parameters to ENTRYPOINT)
```
When used in the shell or exec formats, the `CMD` instruction sets the command to be executed when running the image.  The `CMD` command should be used to run the software contained by your image, along with any arguments. 

`CMD` should almost always be used in exec form. In most other cases, `CMD` should be given an interactive shell for example, `CMD ["perl", "-de0"]`, `CMD ["python"]`, or `CMD ["php", "-a"]`. Using this form means that when you execute something like `docker run -it python` you'll get dropped into a usable shell. See entrypoint for more information about the third use-case of CMD.

## ENTRYPOINT

Entrypoint does something similar to `CMD` but is really completely different. It is really only used in conjunction with `CMD` when the expected users already know how `ENTRYPOINT` works. In most cases it works like this: a bash script is passed to `ENTRYPOINT` and the strings passed to `CMD` are then passed as arguments to the bash script. For example...

```docker
ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["postgres"]
```

When the container starts up, the command to be executed looks like this:

```bash
sh -c 'docker-entrypoint.sh postgres'
```

Some conditional statements can be used on the arguments to initialize the container. The last line of the script must be `exec "#@"`.

```bash
#!/bin/bash
set -e

if [ "$1" = 'postgres' ]; then
    chown -R postgres "$PGDATA"

    if [ -z "$(ls -A "$PGDATA")" ]; then
        gosu postgres initdb
    fi

    exec gosu postgres "$@"
fi

exec "$@"
```

The exec command given becomes the container's PID 1. `$@` is a shell variable that means "all the arguments", so that leaves us with `exec <arguments from cmd>`

Two forms of Entrypoint:


```
ENTRYPOINT ["executable", "param1", "param2"] (exec form, preferred)
ENTRYPOINT command param1 param2 (shell form)
```
```
$ docker run s3cmd
```
// TODO

Commandline arguments to `docker run s3cmd` will be appended after all elements in an exec form `ENTRYPOINT`, and will `CMD`. `docker run s3cmd -d` will pass the `-d` argument to the entry point. **Only the last `ENTRYPOINT` instruction will have an effect**.

## EXPOSE

The `EXPOSE` instruction informs docker that the container listens on the specified network ports at runtime. It functions as a type of documentation between the person who builds the image and the person who runs the container about which ports are intended to be published. 

```
EXPOSE 80/tcp
EXPOSE 80/udp
```

## ADD

The `ADD` instruction copies new files or directories from `<src>` and adds them to the filesystem of the image at the path `<dest>`. The source file can also be a remote file URL.

```

ADD [--chown=<user>:<group>] <src>... <dest>
ADD [--chown=<user>:<group>] ["<src>",... "<dest>"] 
^(this form is required for paths containing whitespace)
```
Each `<src>` may contain wildcards. `<dest>` is an absolute path, or a path relative to `WORKDIR` into which the resource will be copied inside the destination container.

```
ADD hom* /mydir/        # adds all files starting with "hom"
ADD hom?.txt /mydir/    # ? is replaced with any single character, e.g., "home.txt"
```
`ADD` obeys the following rules:
- The `<src>` path must be inside the context of the build.
- If `<src>` is a directory, the entire contents of the directory are copied, including filesystem metadata. 
- If `<src>` is a local tar archive an a recognized compression format then it is unpacked as a directory.
- If `<dest>` does not end with a trailing slash it will be considered a regular file.
- If `<dest>` dpes not exist it will be created.

## COPY

The `COPY` instruction copies new fiules or directories from `<src>` and adds them to the filesystem of the container at the path `<dest>`. 

```
COPY [--chown=<user>:<group>] <src>... <dest>
COPY [--chown=<user>:<group>] ["<src>",... "<dest>"] 
^(this form is required for paths containing whitespace)
```

Does not support remote file URLs or unpacking an archive. Use this anytime you need to copy a file to a container's filesystem unless you specifically need the magic of `ADD`.

## ENV

The `ENV` instruction sets the environment variable `<key>` to `<value>`. This value will be in the environment for all subsequent instructions in the build stage. This is very similar to `ARG` in that they can be explicitly set in the dockerfile. One key difference is that `ENV` variables can only be set from the command-line at **run**time.
```
> docker run --env SSL_PROVIDER=libssl -p 22:2222 -d d0957ffdf8a2
```
```
ENV myName="John Doe" myDog=Rex\ The\ Dog \
    myCat=fluffy
```
The above form uses equal sign in the syntax and allows multiple environment variables to be set on a single line. Quotes and backslashes can be used to include spaces within values. Alternatively, you can use this syntax:

```
ENV myName John Doe
ENV myDog Rex The Dog
ENV myCat fluffy
```

The entire string after the first space will be treated as the `<value>` - including whitespace characters. The environment variables set using `ENV` will persist when a container is run from the resulting image. You can view the values using `docker inspect` and change them using `docker run --env <key>=<value>`.

### Environment replacement

Environment variables, declared with `ENV`, can be used in certain instructions as variables to be interpreted by the Dockerfile.  They are notated in the Dockerfile with the syntax `$variable_name` or `${variable_name}`. One of the uses of environment variables is to conditionally add text into the document.

- `${variable:-word}` indicates that if variable is set then the result will be that value. If variable is not set then word will be the result.
- `${variable:+word}` indicates that if variable is set then word will be the result, otherwise the result is the empty string.

`word` can be any string, including additional environment variables.

### Notes on export

Bash `export` won't persist across images. Don't forget that each Dockerfile directive will generate an intermediate container, committed into an intermediate image. Intermediate images won't preserve the exported value.

Environment variables set using `ENV` will persist when a container is run from the resulting image.

*Example dockerfile:*
```
FROM centos:6
ENV FOO=foofoo
RUN export BAR=barbar
RUN export BAZ=bazbaz && echo "$FOO $BAR $BAZ"
```
*During the build:*
```
 ---> d0957ffdf8a2
Step 2/4 : ENV FOO=foofoo
 ---> Running in 35577961e8f0
Removing intermediate container 35577961e8f0
 ---> 06d3385c2a4a
Step 3/4 : RUN export BAR=barbar
 ---> Running in 9eab6eda74b4
Removing intermediate container 9eab6eda74b4
 ---> 850a1dbdc1da
Step 4/4 : RUN export BAZ=bazbaz && echo "$FOO $BAR $BAZ"
 ---> Running in 53ae04badb02
foofoo  bazbaz
```
*In the resulting container:*
```
> docker run -it --entrypoint /bin/bash 6003e4329300
[root@2eb9e5669fe0 /]# echo $FOO
foofoo
[root@2eb9e5669fe0 /]# echo $bar

[root@2eb9e5669fe0 /]# echo $baz

```

## ARG

A drawback to `ENV` variables is that they cannot be changed during the build (i.e. there is no argument to `docker build` for environment variables). Build arguments are the key to generating environment variables dynamically. 

When choosing between using an `ARG` or an `ENV` all you have to decide is...

> Do you need build-time customization or run-time customization?

If all you need is to run the same image with different settings then `ENV` is the way to go. If you need to build the image differently in order to run it, then use `ARG`.

```
> docker build --build-arg SSL=1 . -f ubuntu_mosquitto
```

*Dockerfile:*
```
ARG SSL
ARG EXPOSE_PORT=${SSL:+8883}
ARG EXPOSE_PORT=${EXPOSE_PORT:-1883}
ENV MOSQ_CONF=${SSL:+"-c /mosquitto/mosquitto-tls.conf"}
# ...
EXPOSE ${EXPOSE_PORT}
CMD ["sh", "-c", "/usr/sbin/mosquitto ${MOSQ_CONF}"]
```

In the above dockerfile, I am letting the user choose whether to build a mosquitto MQTT image with SSL enabled or disabled. If the SSL build argument is specified then `8883` is substituted into `EXPOSE_PORT`. Otherwise, empty string is substituted in.  

Later, SSL is checked again and if it is specified then the environment variable MOSQ_CONF will contain a specified configuration file (the file with tls-specific settings). 

At the end of the file a shell is launched with the mosquitto command and the MOSQ_CONF variable. If SSL was specified, MOSQ_CONF will be substituted with the path to the configuration file with TLS settings, otherwise it will be empty and the default configuration file will be used.

