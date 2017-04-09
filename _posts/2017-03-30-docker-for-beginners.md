---
layout: post
title: Docker for beginners
descrtiption: This post talks about the basic terminology of Docker. It covers the essential concepts to get started with Docker.
category: Docker
---
Surely, if you keep pace with the fast-moving software development world, you have heard the word *Docker* before. What does that mean anyway? Is there anything else I need to know besides their cute whale logo?

<div class="img_container">
  ![Docker logo](http://facilcloud.com/noticias/wp-content/uploads/2015/07/docker-logo.png){:height="400px" width="400px"}
</div>

A few months ago, I went to my first Docker workshop with zero clue of what this platform was available to do. Not even in my wildest dreams I would’ve taught that a few months later I will be working as staff at the same place. Anyway, here is my perspective of this amazing tool you might want to include in your toolbox for sure. Docker is creating a path into the future, resolving several headache pains for the software industry. With no doubt, Docker will be up and running even the in the largest corporations.

The basic keyword you need to know in order to understand Docker is **container**. A container is a stand-alone executable package that includes all the necessary information to run (code, libraries, system configuration). It allows you to isolate the software from its surroundings to avoid conflicts that may result from using different operating systems.

Docker is *lightweight* which means that you run several containers in the same server to bring up different applications at the same time. This containers can be as flexible as possible to fulfill your environment needs (production, staging), you can add very specific libraries to each of your applications.

I will not get into much technical detail on how Docker runs internally, but it is important to clarify the difference between Docker and Virtual Machines. With Docker you can run multiple containers that can share the same OS kernel, while a virtual machine has its own copy of operating system taking almost 100% more space than containers.

<div class="img_container">
  ![Difference_docker_vm](http://gordonsun-blog.s3.amazonaws.com/wp-content/uploads/2015/05/docker-containers-vs-vms.png){:height="300px" width="400px"}
</div>

It may seem a little confusing still, but once you get hand-on experience with the following tutorials you will get a very clear idea of how Docker works.

After this small Docker introduction, it’s time to install [Docker](https://docs.docker.com/engine/getstarted/step_one/ ) on your favorite OS: [Mac](https://docs.docker.com/docker-for-mac/ ), [Linux](https://docs.docker.com/engine/installation/linux/ ) or [Windows](https://docs.docker.com/docker-for-windows/ ).

> **Note**: The previous links on how to install Docker is for MacOS Yosemite or newer and Windows 10 Pro 64-bit only.
If you are using an older version of Windoes or MacOS, you need [Docker Machine](https://docs.docker.com/machine/overview/ ) instead.

Once you have Docker installed, here is a [tutorial](https://github.com/docker/labs/blob/master/beginner/readme.md) for beginners that you can follow to get use to the platform and basic terminology.

### **Running a container**

* Download an Apline Linux container.

```
$ docker pull alpine
```

The `pull` command looks for the Alpine image in the Docker registry and saves a local copy on the system. You can use `docker images` to see which ones you have saved on your system. An *image* is a file system that specifies the configuration to create a container.

```
$ docker images
```

## Stop!

If you are getting a permission denied error that is blocking the way to execute the first pull command, you better verify the installation. If you don't, go ahead and skip this part.

```
$ docker --version

Docker version 17.03.0-ce, build 60ccb22
```
If Docker is properly installed and you are stil getting the same permission error, you may need to prefix your Docker commands with `sudo`.

```
$ sudo docker pull alpine

Using default tag: latest
latest: Pulling from library/alpine
627beaf3eaaf: Pull complete
Digest: sha256:58e1a1bb75db1b5a24a462dd5e2915277ea06438c3f105138f97eb53149673c4
Status: Downloaded newer image for alpine:latest
```

Problem solved.

* Run the Alpine Linux container.

```
$ sudo docker run alpine ls -l

total 52
drwxr-xr-x    2 root     root          4096 Mar  3 11:20 bin
drwxr-xr-x    5 root     root           340 Mar 27 02:40 dev
drwxr-xr-x   14 root     root          4096 Mar 27 02:40 etc
drwxr-xr-x    2 root     root          4096 Mar  3 11:20 home
drwxr-xr-x    5 root     root          4096 Mar  3 11:20 lib
drwxr-xr-x    5 root     root          4096 Mar  3 11:20 media
drwxr-xr-x    2 root     root          4096 Mar  3 11:20 mnt
dr-xr-xr-x  244 root     root             0 Mar 27 02:40 proc
drwx------    2 root     root          4096 Mar  3 11:20 root
drwxr-xr-x    2 root     root          4096 Mar  3 11:20 run
drwxr-xr-x    2 root     root          4096 Mar  3 11:20 sbin
drwxr-xr-x    2 root     root          4096 Mar  3 11:20 srv
dr-xr-xr-x   13 root     root             0 Mar 27 02:40 sys
drwxrwxrwt    2 root     root          4096 Mar  3 11:20 tmp
drwxr-xr-x    7 root     root          4096 Mar  3 11:20 usr
drwxr-xr-x   12 root     root          4096 Mar  3 11:20 var
```

When you call `run`, Docker looks for the image you specified, creates the container and runs a command inside the container (in this case `ls -l`). Docker just displayed the list as you requested.

* Run another command line from the Alpine Linux container.

```
$ sudo docker run alpine echo “hello from alpine”

hello from alpine
```

The Docker container just outputed the string we gave and then exited. You can appreciate how quickly it is to work with containers now. Imagine how long it would take to do that with a VM!

* Working with interactive shells inside containers

```
$ sudo docker run alpine /bin/sh
```

It looks like nothing happened, maybe something is missing? Not at all. The interactive shell will exit after performing the command you gave them, *unless* you add the interactive terminal flag `-it`

```
$ sudo docker run -it alpine /bin/sh
```

Now, you are inside the container shell and you can perform the command we saw before ls -l and much more. To get out of the container shell, just `exit`.

* List all the containers currently running

```
$ sudo docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
```

Surely, you just saw a blank table like the one on top. The reason is that every time we ran a command the container exited when it was finished.

* List all the containers available

```
$ sudo docker ps -a

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
cd197867be95        alpine              "/bin/sh"                11 minutes ago      Exited (0) 11 minutes ago                       kickass_joliot
be35beba6381        alpine              "/bin/sh"                14 minutes ago      Exited (0) 14 minutes ago                       eloquent_kalam
53b23230d97f        alpine              "echo 'Hello from ..."   16 minutes ago      Exited (0) 16 minutes ago                       peaceful_thompson
8a758836de4c        alpine              "ls -l"                  19 minutes ago      Exited (0) 19 minutes ago                       upbeat_almeida
```

If you need more information about Docker, use `docker run --help` to see all the flags available.

Congratulations! You just finished your first Docker tutorial for beginners and learn some basic terminology.

See you on the next post!
