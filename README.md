# DockerTutorial

This short tutorial can be used to understand all the most important topics related to Docker. 

### Dockerfile

The Dockerfile (capital D, no extension) contains a list of instructions (steps) that are sent to the docker daemon. Always remember that the docker daemon runs on our local machine and the docker CLI is used to communicate with the daemon.

Those are the very first commands inside the Dockerfile:

```
FROM alpine:latest

RUN mkdir /home/programming
```

The `FROM` command is used to specify the underlying OS that we are going to use to build our image. In this case the image will be built on top of the latest version of linux alpine.

The `RUN` command is used to execute a shell command inside the image created. In this case we are executing two commands, a mkdir to create a folder called `programming` inside the home directory and anoter command to install `bash` because it's not present in linux alpine.

The directory is created inside the docker image space and not inside the machine where we run the command.

### Docker image

We can create a docker image from a Dockerfile using the build command. `docker build --tag my_image .` generate a docker image from the Dockerfile present in the current directory (the `tag` attribute is used to give a name to this image). 

When we run the build command the instruction inside the Dockerfile are sent to the daemon and the output will be something like that:

```
$ docker build -t my_image .
Sending build context to Docker daemon   55.3kB
Step 1/2 : FROM alpine:latest
latest: Pulling from library/alpine
9d48c3bd43c5: Pull complete
Digest: sha256:72c42ed48c3a2db31b7dafe17d275b634664a708d901ec9fd57b1529280f01fb
Status: Downloaded newer image for alpine:latest
 ---> 961769676411
Step 2/2 : RUN mkdir /home/programming
 ---> Running in 4498eebdae5b
Removing intermediate container 4498eebdae5b
 ---> dbb97af0abbe
Successfully built dbb97af0abbe
Successfully tagged my_image:latest
```

As we can see the step 1/2 download the alpine image (from an online repository called Docker Hub) and the step 2/2 run the mkdir command. The alpine image is downloaded only if is not present in the local repository so if we run the same command twice the second execution will skip the download.

If we list all the generated images with `docker images` we'll see the image created called `my_image` and the downloaded alpine image.

```
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
my_image            latest              dbb97af0abbe        2 minutes ago       5.58MB
alpine              latest              961769676411        2 months ago        5.58MB
```

### Docker container

Once we have an image we can create a container out of it. The command in this case is `docker run`.
The full command in this case is `docker run -itd --name my_container my_image`, this command will create a container named `my_container` from the image `my_image` previously generated. 

At this point we should have a running docker container called, and if we run the command `docker ps -a` we should get this output:

```
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS              PORTS               NAMES
55f337aad2bc        my_image            "/bin/sh"           About a minute ago   Up About a minute                       my_container
```

To understand what we have created we can run the command `docker exec -it my_container bash`. This command open a terminal inside our container. If we run a `ls` we'll see all the folder structure we usually found in a linux environment and if we enter the `/home` folder we can find the `/programming` folder created with the RUN command inside the Dockerfile.

