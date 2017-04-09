---
layout: post
title: Running a simple webapp with Docker
description: This post talks about how to create a web application using Docker. It also covers how to create and upload your Docker image.
category: Docker
---

This tutorial is based on **Webapps with Docker** by [Docker Labs](https://github.com/docker/labs/blob/master/beginner/chapters/webapps.md).

* Running a static website container

After studying the basic [terminology](https://livier92.github.io/docker-for-beginners.html) of Docker is time to apply it to to real world applications - deploying a webapp with Docker.

First, download and run an image from Docker Hub, which is used to create a single-page website. Implicitly, Docker does a pull if the image can not be found locally. Then, Docker will run the container in detached mode by using `-d`, which detaches the container from the terminal and returns where the container started.

```
$ sudo docker run -d seqvence/static-site
```

An important question may rise after the creation of this container: In what port is my server running and how can I access that container?

First, stop the container by retrieving the container ID. Since we are in detached mode, we don’t have to run Docker in another terminal.

```
$ sudo docker ps

CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS               NAMES
9df5b45731b6        seqvence/static-site   "/bin/sh -c 'cd /u..."   4 minutes ago       Up 4 minutes        80/tcp, 443/tcp     zealous_murdock
```

Then, look at the container ID, in my case is *9df5b45731b6*. To remove the container use `docker stop` followed by `docker rm`.

```
$ sudo docker stop 9df5b45731b6
$ sudo docker rm 9df5b45731b6
```

You don’t have specify the container id, just the ones needed to uniquely identify which container you want to remove.

Now, create a Docker container with more specific parameters.

```
$ sudo docker run --name static-site -e AUTHOR="Your Name" -d -P seqvence/static-site
```

Parameters:

- -**d**: create a container with the detached mode
- -**P**: will publish all the exposed container ports on random ports on the Docker host
- -**e**: pass environment variables to the container
- --**name**: allows you to give a name to the container
- **AUTHOR**: an environment variable with the value.

To see all the exposed ports from your container, run the `docker port`.

```
$ sudo docker port static-site

443/tcp -> 0.0.0.0:32768
80/tcp -> 0.0.0.0:32769
```

In this case, to see the web app we have to go to http://localhost:32769. Did you checked out your new web page?

Now remove the previous container, since we will not use it anymore. A shortcut to remove the container using `-f`.

```
$ sudo docker rm -f static-site
```

Confirm your deletion with `docker ps`.

You can also run a second webserver at the same time, specifying a custom host port mapping to the container's webserver.

```
$ docker run --name static-site-2 -e AUTHOR="Your Name" -d -p 8888:80 dockersamples/static-site
```

To deploy this on a real server you would just need to install Docker, and run the above docker command (as in this case you can see the AUTHOR is Docker which we passed as an environment variable).

* Using Docker images

Having available images on Docker Hub can save you a lot of work. But what if you want to build your own image to build your app locally and then push it to Docker Hub?

Remember that we use `docker images` to see which ones we have saved locally.

```
$ sudo docker images

REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
alpine                 latest              4a415e366388        3 weeks ago         3.99 MB
node                   4                   3cbf9178f474        7 months ago        648 MB
node                   0.12                39e0208edb6d        7 months ago        643 MB
redis                  3.0                 22d1a4cb1939        8 months ago        178 MB
mongo                  3.0                 deed66f6ca09        8 months ago        271 MB
hello-world            latest              c54a2cc56cbb        9 months ago        1.85 kB
seqvence/static-site   latest              f589ccde7957        12 months ago       191 MB
```

To make an analogy with git, images also work like repositories. You can commit changes to them and have different versions. If you don’t specify which version you want to pull, the client will download the latest. To specify a version you can use `docker_image:verison`.

```
$ sudo docker ubuntu:12.04
```

You can also search for docker images from the command line using `docker search`.

```
$ sudo docker search
```

Okay, time to create a new image that sandboxes a Flask application. The objective of this app will be to display a random cat picture with Python Flask and we will add a *Dockerfile* to it.

We will create the following file under a directory named *flask-app*.

- app.py
- requirements.txt
- templates/index.html
- Dockerfile

Copy and paste the following content in *app.py*.

```
app = Flask(__name__)

# list of cat images
images = [
    "http://ak-hdl.buzzfed.com/static/2013-10/enhanced/webdr05/15/9/anigif_enhanced-buzz-26388-1381844103-11.gif",
    "http://ak-hdl.buzzfed.com/static/2013-10/enhanced/webdr01/15/9/anigif_enhanced-buzz-31540-1381844535-8.gif",
    "http://ak-hdl.buzzfed.com/static/2013-10/enhanced/webdr05/15/9/anigif_enhanced-buzz-26390-1381844163-18.gif",
    "http://ak-hdl.buzzfed.com/static/2013-10/enhanced/webdr06/15/10/anigif_enhanced-buzz-1376-1381846217-0.gif",
    "http://ak-hdl.buzzfed.com/static/2013-10/enhanced/webdr03/15/9/anigif_enhanced-buzz-3391-1381844336-26.gif",
    "http://ak-hdl.buzzfed.com/static/2013-10/enhanced/webdr06/15/10/anigif_enhanced-buzz-29111-1381845968-0.gif",
    "http://ak-hdl.buzzfed.com/static/2013-10/enhanced/webdr03/15/9/anigif_enhanced-buzz-3409-1381844582-13.gif",
    "http://ak-hdl.buzzfed.com/static/2013-10/enhanced/webdr02/15/9/anigif_enhanced-buzz-19667-1381844937-10.gif",
    "http://ak-hdl.buzzfed.com/static/2013-10/enhanced/webdr05/15/9/anigif_enhanced-buzz-26358-1381845043-13.gif",
    "http://ak-hdl.buzzfed.com/static/2013-10/enhanced/webdr06/15/9/anigif_enhanced-buzz-18774-1381844645-6.gif",
    "http://ak-hdl.buzzfed.com/static/2013-10/enhanced/webdr06/15/9/anigif_enhanced-buzz-25158-1381844793-0.gif",
    "http://ak-hdl.buzzfed.com/static/2013-10/enhanced/webdr03/15/10/anigif_enhanced-buzz-11980-1381846269-1.gif"
]

@app.route('/')
def index():
    url = random.choice(images)
    return render_template('index.html', url=url)

if __name__ == "__main__":
    app.run(host="0.0.0.0")
```

To install the Python module necessary for the app, add the following content to the *requirements.txt*.

```
Flask==0.10.1
```

Inside the *templates* file, copy and paste the following content in *index.html*.

```
<html>
  <head>
    <style type="text/css">
      body {
        background: black;
        color: white;
      }
      div.container {
        max-width: 500px;
        margin: 100px auto;
        border: 20px solid white;
        padding: 10px;
        text-align: center;
      }
      h4 {
        text-transform: uppercase;
      }
    </style>
  </head>
  <body>
    <div class="container">
      <h4>Cat Gif of the day</h4>
      <img src="{{url}}" />
      <p><small>Courtesy: <a href="http://www.buzzfeed.com/copyranter/the-best-cat-gif-post-in-the-history-of-cat-gifs">Buzzfeed</a></small></p>
    </div>
  </body>
</html>
```

Now that our app has a structure, move on to the *Dockerfile*. Our image will be based on a base image. Since our app is written in Python, we will build our own Python image based on Alpine.

The *Dockerfile* is a text file that has a set of commands that the Docker daemon will execute to create the image. The *Dockerfile* needs to contain all the information needed to create it (a base Docker image to run from, location of your project, dependencies, commands to run at start-up). This is a way to automate a process for image creation.

Follow this steps to add content to the *Dockerfile*.

1. Specify the base image, using `FROM` keyword.

    ```
    FROM alpine:3.5
    ```

    The **FROM** keyword defines the base layer for the new image. It takes the name of the image as an argument.

2. Install Python pip package and its dependencies.

    ```
    RUN apk add --update py2-pip
    ```

    The **RUN** keyword is used to build up the image you are creating. Docker will run and create a new image for each `RUN` command. You can rollback to previous states of that image.The syntax is to place the full text of the shell command and this will automatically run in */bin/sh shell*. You can pick a different shell like  `RUN /bin/bash -c 'mkdir /user/local/foo'`.

    Now, add the files the make the Flask Application

3. Install the Python requirements for our app to run.

    ```
    COPY requirements.txt /usr/src/app/
    RUN pip install --no-cache-dir -r /usr/src/app/requirements.txt
    ```

    The **COPY** keyword is to copy local files to a container.

4. Copy the files created earlier into the image.

    ```
    COPY app.py /usr/src/app/
    COPY templates/index.html /usr/src/app/templates/
    ```

5. Expose the port number where the app is running.

    ```
    EXPOSE 5000
    ```

    The **EXPOSE** keyword shows which ports provides services. This information can be retrieved via `docker inspect <container-id>`.

6. Add the command to run the application.

    ```
    CMD ["python", "/usr/src/app/app.py"]
    ```

    **CMD** is used to tell the container which command to run at start-up. This command does not create a new layer. There is only one *CMD* per *Dockerfile*. If you need to run multiple commands than you better run a script. *CMD* requires to tell you where to run the command unlike *RUN*.

Now that the configuration is set up on *Dockerfile*, it is time to build the image.

```
$ sudo docker build -t <YOUR_USERNAME>/myfirstapp .
```

Don’t forget to replace your username on the command above for the tag flag. Be sure to put the same one as your Docker hub account. If you don’t have one, please go ahead and do [one](https://hub.docker.com/). Also you are specifying the location of the *Dockerfile* with the period (which is the current directory).

Check if your docker image was created by running `docker images` and run the image.

```
$ sudo docker run -p 8888:5000 --name myfirstapp YOUR_USERNAME/myfirstapp
```

Head over to *http://localhost:8888* and your app should be live.

You can push your image to the Docker hub like a git repository.

```
$ sudo docker push YOUR_USERNAME/myfirstapp
```

If you get an issue similar to `denied: requested access to the resource is denied` then you need to login from the terminal with your Docker credentials.

```
$ sudo docker login
```

Type your credentials and re-try the previous command again.

That’s it. Hope you learn new stuff with this tutorial and don’t forget to add a comment for any issue or concern.
