# Docker Cheat Sheet


## Basics
Creating and running a docker container by supplying an image name
`docker run <image-name>`

Creating and running a docker container and supplying an override command
`docker run <image-name> <command to execute after container is running>`

Listing all the running docker containers
`docker ps`

Get a history of all the containers created
`docker ps --all`

Issuing the command `docker run` is essentially the same as running
`docker create` then `docker start`

To create a docker container we execute
`docker create <image-name>`

Executing this command will prep the container and spit out a container ID

To run a created docker container, we execute
`docker start <container-id>`

In order to watch the console output of the container we need to attach to the container with `-a`
`docker start -a <container-id>`

Conatiners that have the exited status can be re-started with `docker start` and supplying the id
we got from running `docker ps`. Of course if you want to be able to see the output, you will have
to use the `-a` flag as mentioned above

When a container exits, it is not removed from the file system. In order to remove the container and
free up disk space we can run:
`docker system prune`

This will remove any stopped containers, any networks that are not in use by a container, any dangling images
and the build cache (meaning that images will have to be remade or redownloaded)

In order to retrieve all the output of the container that was run we execute
`docker logs <container-id>`

In order to gracefully stop a container (allow for cleanup or saving files, etc.) we run the command
`docker stop <container-id>`

In order to kill a container (stop the container as soon as the command is issued) we run
`docker kill <container-id>`

The kill command is generally reserved for when the process inside a container has locked up or has hung
However, the stop command only allows 10 seconds of additional run time to clean up otherwise it falls back
to the kill command

In order to execute an additional command along with attaching user input inside a docker container we run
`docker exec -it <container-id> <command>`

The `-it` argument allows us to type input directly into the container

The `-it` argument is 100% equivalent to running `-i` and `-t` separately
`-i` tells docker that we want to attach out terminal to the containers' STDIN channel
`-t` tells docker that we want full terminal functionality and formatting instead of just raw input/output

The command can be run without the `-it` flag, however this will not allow for user input

e.g. `docker exec <container-id> <command>` will execute the written command, but if the program requires user
input, it will exit out immediately since there is no capacity for user input without the `-it` command argument

In order to gain shell access to a container we run
`docker exec -it <container-id> sh`

To get out of a container shell we press `Ctrl-D`

We can also use the `-it` flag along side the `docker run` command to execute a command in the conatiner as soon
as it starts up, however this will displace any command that was supposed to run on startup

## Creating Your Own Docker Images

All the configuration for a new Docker Image is contained in the DockerFile

First we specify a base image
`FROM alpine`

This specifies the docker image that we want to use as a base

Then we run some commands to install additional programs
`RUN apk add --update redis`

This is used to execute a custom command while the image is being built

Then specify a command to run when the container starts
`CMD [ "redis-server" ]`

Then we build the image using `docker build /path/to/Dockerfile`
or we can just run `docker build .` if the Dockerfile is in the current working directory of the terminal

To generate an image from a container we run
`docker commit <container-id>`

In order to specify a default command we can add `-c 'CMD ["command to run"]` after `docker commit`
So the full command would look something like `docker commit -c 'CMD ["command to run"] <container-id>`

To copy files or directories into the container we specify the folloring command
`COPY path/to/copy/from destination/directory/in/container`
The path that we are copying from is relative to the build context, so wherever the Dockerfile is

In most cases we will want to also specify a proper working directory inside the container
we can do that with the following
`WORKDIR /path/to/dir`

### Port Mapping

In order to allow network traffic into the container we need to map the port where the data is coming from
to the port on the container where the it expects data to be coming in
To do this we use a docker run command with port mapping using the `-p` flag
`docker run -p <incoming-port>:<container-port> <image-id/name>`

### Tagging the built Docker Image

In order to tag a built image instead of having to reference it by it's automatically generated ID we supply the `-t` argument
Followed by the name of the image
The general naming convention for tagging an image is `<your-docker-id> / <project/repo-name> : <version>`
As such the name for the exaple image would be `slothic/redis:latest`
And so the full command we run is `docker build -t slothic/redis:latest .`


