## Docker images for OpenCV

Here are Dockerfiles to get you up and running with a OpenCV machine.

## Specs
This is what you get out of the box when you create a container with the provided image/Dockerfile:
* Ubuntu 14.04
* [OpenCV] (http://opencv.org/)
* A few common libraries used for deep learning

## Setup
### Prerequisites
1. Install Docker following the installation guide for your platform: [https://docs.docker.com/engine/installation/](https://docs.docker.com/engine/installation/)

#### Build the Docker image locally
You can build the images locally. Note that this will take an hour or two depending on your machine since it compiles a few libraries from scratch.

```bash
git clone https://github.com/icarofua/opencv_docker.git
cd opencv_docker
```	

**OpenCV GIT Version**
```bash
docker build -t icarofua/opencv:git -f Dockerfile_opencv_git .
```

**OpenCV 3.0 Version**
```bash
docker build -t icarofua/opencv:3.0 -f Dockerfile_opencv_3.0 .
```

**OpenCV 3.1 Version**
```bash
docker build -t icarofua/opencv:3.1 -f Dockerfile_opencv_3.1 .
```


This will build a Docker image named `opencv` and tagged either `3.1`, `3.0` or `git` depending on the tag your specify.

## Running the Docker image as a Container
Once we've built the image.

**OpenCV GIT Version**
```bash
docker run -it -v /sharedfolder:/root/sharedfolder icarofua/opencv:git bash
```

**OpenCV 3.1 Version**
```bash
docker run -it -v /sharedfolder:/root/sharedfolder icarofua/opencv:3.1 bash
```

**OpenCV 3.0 Version**
```bash
docker run -it -v /sharedfolder:/root/sharedfolder icarofua/opencv:3.0 bash
```
	
| Parameter      | Explanation |
|----------------|-------------|
|`-it`             | This creates an interactive terminal you can use to iteract with your container |
|`-v /sharedfolder:/root/sharedfolder/` | This shares the folder `/sharedfolder` on your host machine to `/root/sharedfolder/` inside your container. Any data written to this folder by the container will be persistent. You can modify this to anything of the format `-v /local/shared/folder:/shared/folder/in/container/`. See [Docker container persistence](#docker-container-persistence)
|`icarofua/opencv:git`   | This the image that you want to run. The format is `image:tag`. In our case, we use the image `opencv` and tag `git`, `3.1` or `3.0` to spin up the appropriate image |
|`bash`       | This provides the default command when the container is started. Even if this was not provided, bash is the default command and just starts a Bash session. You can modify this to be whatever you'd like to be executed when your container starts.

### Data Sharing
See [Docker container persistence](#docker-container-persistence). 
Consider this: You have a script that you've written on your host machine. You want to run this in the container and get the output data (say, a trained model) back into your host. The way to do this is using a [Shared Volumne](#docker-container-persistence). By passing in the `-v /sharedfolder/:/root/sharedfolder` to the CLI, we are sharing the folder between the host and the container, with persistence. You could copy your script into `/sharedfolder` folder on the host, execute your script from inside the container (located at `/root/sharedfolder`) and write the results data back to the same folder. This data will be accessible even after you kill the container.

## What is Docker?
[Docker](https://www.docker.com/what-docker) itself has a great answer to this question.

Docker is based on the idea that one can package code along with its dependencies into a self-contained unit. In this case, we start with a base Ubuntu 14.04 image, a bare minimum OS. When we build our initial Docker image using `docker build`, we install all the deep learning frameworks and its dependencies on the base, as defined by the `Dockerfile`. This gives us an image which has all the packages we need installed in it. We can now spin up as many instances of this image as we like, using the `docker run` command. Each instance is called a _container_. Each of these containers can be thought of as a fully functional and isolated OS with all the deep learning libraries installed in it. 

## Why do I need a Docker?
Installing all the deep learning frameworks to coexist and function correctly is an exercise in dependency hell. Unfortunately, given the current state of DL development and research, it is almost impossible to rely on just one framework. This Docker is intended to provide a solution for this use case.

### Docker container persistence
Keep in mind that the changes made inside Docker container are not persistent. Lets say you spun up a Docker container, added and deleted a few files and then kill the container. The next time you spin up a container using the same image, all your previous changes will be lost and you will be presented with a fresh instance of the image. This is great, because if you mess up your container, you can always kill it and start afresh again. It's bad if you don't know/understand this and forget to save your work before killing the container. There are a couple of ways to work around this:

1. **Commit**: If you make changes to the image itself (say, install a few new libraries), you can commit the changes and settings into a new image. Note that this will create a new image, which will take a few GBs space on your disk. In your next session, you can create a container from this new image. For details on commit, see [Docker's documentaion](https://docs.docker.com/engine/reference/commandline/commit/).

2. **Shared volume**: If you don't make changes to the image itself, but only create data, then commiting the image each time is an overkill. In this case, it is easier to persist the data changes to a folder on your host OS using shared volumes. Simple put, the way this works is you share a folder from your host into the container. Any changes made to the contents of this folder from inside the container will persist, even after the container is killed. For more details, see Docker's docs on [Managing data in containers](https://docs.docker.com/engine/userguide/containers/dockervolumes/)
 
### What operating systems are supported?
Docker is supported on all the OSes mentioned here: [Install Docker Engine](https://docs.docker.com/engine/installation/) (i.e. different flavors of Linux, Windows and OS X). All versions will run on all the above operating systems.
