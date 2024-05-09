# Docker on Linux for AWS

![image](https://github.com/Matt4llan/Docker-AWS/assets/156334555/de8a4a76-626b-49f7-9622-53f356bacb9c)

## Objective

The objective of this tutorial is to get up and running with Docker on Linux using an AWS virtual machine. I will work with images from the public Docker registry, run a handful of containers, and create your my image from which to create containers. 

### Skills Learned

By completing this lab, I will gain hands-on experience with installing Docker on Linux using an AWS virtual machine, Adding a user to the Docker group, finding and useing images from the public Docker Registry and building my own images using Dockerfiles

### Tools Used

- AWS - Amazon Cloud Hosting Platform
- Docker - To create the containers
- Nano - Text Editorc

## Step 1 - Logging In to the Amazon Web Services Console

The AWS Management Console is a web control panel for managing all your AWS resources, from EC2 instances to SNS topics. The console enables cloud management for all aspects of the AWS account, including managing security credentials and even setting up new IAM Users.

![image](https://github.com/Matt4llan/Docker-AWS/assets/156334555/85cae333-8eb7-4672-9641-3ecf43299772)

## Step 2 - Connecting to the Virtual Machine using EC2 Instance Connect

In the AWS Management Console search bar, enter EC2, and click the EC2 result under Services, to see available instances, click Instances in the left-hand menu

![image](https://github.com/Matt4llan/Docker-AWS/assets/156334555/d43fca7d-ecc6-4a2c-9ba4-9e41e2086b36)

To open a browser-based shell, click Connect

![image](https://github.com/Matt4llan/Docker-AWS/assets/156334555/e4801a2f-3bc7-442b-bf8c-9273d413f1e9)

## Step 3 - Installing Docker on Amazon Linux

I will install Docker using the yum package manager that is available on Amazon Linux. Docker comes in two flavors: Community Edition (CE) and Enterprise Edition (EE). The community edition is open source and available free of charge. It includes the core Docker functionality and was previously called "Docker Engine." The enterprise edition requires a subscription and includes a support package, a certification program to create trusted containers and extensions, and other features. I will be installing the community edition.

Use the following command to install Docker:

```
sudo yum -y install docker
```

![image](https://github.com/Matt4llan/Docker-AWS/assets/156334555/325b5839-236e-43b9-90aa-b69b07d8941b)

Use the following command to start Docker as a service:

```
sudo systemctl start docker
```

To verify Docker is running enter:

```
sudo docker info
```

This will output system-wide information about Docker as seen below. You can see some useful information, such as the number of containers, and the version of the Docker server. Docker adopts a client-server architecture, so the server doesn't have to be running on the same host as the client. In your case, you are using the Docker command line interface (CLI) client to connect to the server, called the Docker daemon.

![image](https://github.com/Matt4llan/Docker-AWS/assets/156334555/7b9381d5-27cf-4897-af2b-74237990f516)

## Step 4 - Using Docker without Root Permission on Linux

In the last Step, i used root permissions to run a Docker command. In this Step i will show how to use Docker commands without elevating to root permissions. This may not always be what you want and should only be performed for trusted users. This is because the actions are equivalent to granting root permissions to a user. It will be convenient in the following Steps to not have to enter sudo for every command.

First lets try entering the same command without using root (sudo) permission

```
docker info
```

![image](https://github.com/Matt4llan/Docker-AWS/assets/156334555/d465e722-10ab-4b15-9f81-b3e82e437182)

We received a permission denied error message as By default, the Docker daemon will reject requests from users that aren't part of the docker group.

First lets verify the docker group exists by searching for it in the groups file

```
grep docker /etc/group
```

![image](https://github.com/Matt4llan/Docker-AWS/assets/156334555/049e634c-b07f-4516-90ef-41af98461aa0)

If you don't see a line beginning with "docker:" like i did above, you will need to add the group yourself by entering:

```
sudo groupadd docker
```

Lets add our user to the Docker group

```
sudo gpasswd -a $USER docker
```

We need to login again to have your groups updated by entering

```
newgrp docker
```

Lets verify that my user can successfully issue Docker commands by entering

```
docker info
```

![image](https://github.com/Matt4llan/Docker-AWS/assets/156334555/866a8b41-3ce8-4f20-a33b-bc59d148501c)

Success!! no error this time

## Step 5 - Getting Docker Help from the Command Line

The commands are organized into common commands and a more exhaustive list grouped around the management of a specific component of Docker. You use each with a different syntax. For a common command, the usage is:

```
docker command-name [options] 
```

and the usage for a management group command is:

```
docker management-group command-name [options]
```

To see a list of the commands in Docker, simply enter:

```
docker --help
```

## Step 6 - Running a Docker Container

Containers run a command on top of an image. There are many available images to choose from. Images are collected in repositories. A repository can have many versions of an image. Tags are used to manage versions. You will use images from repositories inside the default Docker Registry, Docker Hub. Docker Hub hosts official images as well as community-contributed images. Docker Hub offers free and paid accounts. You get an unlimited amount of public repositories and one free private repository with the free account.

To get a container its as simple as entering:

```
docker run hello-world
```

![image](https://github.com/Matt4llan/Docker-AWS/assets/156334555/d3a43af6-7e74-49eb-84f7-2a39c48b2b08)

In the first line, Docker is telling us that it couldn't find the image you specified, hello-world, on the Docker Daemon's local host. The latest portion after the colon (:) is a tag. The tag identifies which version of the image to use. By default, it looks for the latest version.
In the next line, it notifies you that it automatically pulled the image. You could manually perform that task using the command docker pull hello-world. The library/hello-world is the repository it's pulling from inside the Docker Hub registry. library is the account name for official Docker images. In general, images will come from repositories identified using the pattern account/repository.
The last three lines confirm the pull completed and the image has been downloaded.

If we re-run the same command:

```
docker run hello-world
```

![image](https://github.com/Matt4llan/Docker-AWS/assets/156334555/7f27f376-c322-41be-adbb-02a7067d6a91)

Notice this time the Docker output is not included. That is because the specific version of the image was found locally and there is no need to pull the image again.

Lets try running a more complex container with some options specified:

```
docker run --name web-server -d -p 8080:80 nginx:1.12
```

![image](https://github.com/Matt4llan/Docker-AWS/assets/156334555/97d119e2-d4af-4a62-9e7b-1b94c765911a)

This time we specified the tag 1.12 indicating you want version 1.12 of nginx instead of the default latest version. There are three Pull complete messages this time, indicating the image has three layers. The last line is the id of the running container. The meanings of the command options are:

  --name container_name: | Label the container container_name. In the command above, the container is labeled web-server. This is much more manageable than the id, 31f2b6715... in the output above.
  -d: | Detach the container by running it in the background and print its container id. Without this, the shell would be attached to the running container command and you wouldn't have the shell returned to you to enter more commands.
  -p host_port:container_port: | Publish the container's port number container_port to the host's port number host_port. This connects the host's port 8080 to the container port 80 (http) in the nginx command.

Verify the web server is running and accessible on the host port of 8080:

```
docker run --name web-server -d -p 8080:80 nginx:1.12
```

![image](https://github.com/Matt4llan/Docker-AWS/assets/156334555/776e6054-d77f-4cd4-9052-e7a97b98a5df)

To list all running containers, enter:

```
docker ps
```

![image](https://github.com/Matt4llan/Docker-AWS/assets/156334555/0a806d3a-2235-477b-ad5d-5515dd8191e8)

We can enter the following to see a list of all running and stopped containers:

```
docker ps -a
```

![image](https://github.com/Matt4llan/Docker-AWS/assets/156334555/3181687c-cb6d-41b5-9bb0-5cff60c75a04)

To stop the nginx server, enter:

```
docker stop web-server
```

To start running the command in the web-server container again, enter:

```
docker start web-server
```

## Step 7 - Creating our First Docker Image

Docker containers run on top of images. we have seen how to use images on Docker's public registry, the Docker Hub. There are many different images available. It is worth trying to find existing images when you can. Inevitably, you will need to create your own images when making your own applications. In that case, you will still want to invest time into finding the right base layer to add your own layer(s) on top of.

There are a couple of ways to make images. You can use docker commit to create an image from a container's changes. The changes may come from using exec to open a shell in the container like in the previous Lab Step. The other method is using a Dockerfile. A Dockerfile is easier to maintain, easier to repeatedly create images from, and distributions easier.

Dockerfiles specify a sequence of instructions. Instructions can install software, expose network ports, set the default command for running a container using the image, and other tasks. Instructions can really handle anything required to configure the application. Many of the instructions add layers. It is usually a good idea to keep the number of layers to a reasonable number. There is overhead with each layer, and the total number of layers in an image is limited. When the Dockerfile is ready, you can create the image using the docker build command.

We will start by creating an image of a Python Flask web app, the choice of Python as the example app is arbitrary. We will not focus on the specifics of the programming language. We should be able to repeat the process for any other programming language by following a similar process. Whatever programming language or framework you are working with, you should consult the Docker Hub documentation for the image, as it will usually include advice on how to structure your Dockerfile.

To start we will istall Git:

```
sudo yum -y install git
```

Next we will Clone the code repository to your virtual machine:

```
git clone https://github.com/cloudacademy/flask-content-advisor.git
```

![image](https://github.com/Matt4llan/Docker-AWS/assets/156334555/685acf53-c8f9-4d06-81d1-ded21e169d52)

Change to the apps directory:

```
cd flask-content-advisor
```

![image](https://github.com/Matt4llan/Docker-AWS/assets/156334555/53b13ad0-ebf6-4aad-81b6-0c9c677dcff2)

Create and start editing a Dockerfile using the nano text editor:

```
nano Dockerfile
```

Then enter the following into the file:

```
# Python v3 base layer
FROM python:3
# Set the working directory in the image's file system
WORKDIR /usr/src/app
# Copy everything in the host working directory to the container's directory
COPY . .
# Install code dependencies in requirements.txt
RUN pip install --no-cache-dir -r requirements.txt
# Indicate that the server will be listening on port 5000
EXPOSE 5000
# Set the default command to run the app
CMD [ "python", "src/app.py" ]
```

![image](https://github.com/Matt4llan/Docker-AWS/assets/156334555/e37aa446-77a8-4da9-ac64-9365d94191f1)

The lines beginning with # are comments and explain what each instruction is doing. Make sure you read the comments. Some highlights are:

  FROM | sets the base layer image
  COPY . . | copies all of the files in the code repository into the container's /usr/src/app directory
  RUN | executes a command in a new layer at the top of the image
  EXPOSE | only indicates what port the container will be listening on, it doesn't automatically open the port on the container
  CMD | sets the default command to run when a container is made from the image

Once you have all of the instructions in the Dockerfile, press Ctrl+x, enter Y when prompted to save, and press enter.

You are now back at the shell prompt.

To build the image from the Dockerfile enter:

```
docker build -t flask-content-advisor:latest .
```

The -t tells Docker to tag the image with the name flask-content-advisor and tag latest. The . at the end tells Docker to look for a Dockerfile in the current directory. Docker will report what it's doing to build the image. Each instruction has its own step. Steps one and four take longer than the others. Step one needs to pull several layers for the Python 3 base layer image and Step four downloads code dependencies for the Flask web application framework. Notice that each Step ends with a notice that an intermediate container was removed. Because layers are read-only, Docker needs to create a container for each instruction. When the instruction is complete, Docker commits it to a layer in the image and discards the container.

Record your VM's public IP address:

```
curl ipecho.net/plain; echo
```

We will need our IP to test that the web app is available with your browser.  The echo is only to put the shell prompt on a new line.

![image](https://github.com/Matt4llan/Docker-AWS/assets/156334555/1f003e68-c03c-4579-9ceb-c35127898cdc)

Next we can open a new browser tab and navigate to the public IP address we just recorded. The browser will fail to load anything since no server is running yet. Keep the tab open for later.

![image](https://github.com/Matt4llan/Docker-AWS/assets/156334555/b711e75d-df27-43dc-a26e-cb40bb361a71)

Now we can run a container using the image you just built:

```
docker run --name advisor -p 80:5000 flask-content-advisor
```

This runs a container named advisor and maps the container's port 5000 to the host's port 80 (http). This time you didn't include -d to run in detached mode.  That is why you see output and you don't have the shell prompt returned to you. If you did run with -d, you could get the same information from docker logs.

![image](https://github.com/Matt4llan/Docker-AWS/assets/156334555/0e0839fd-6db7-4bee-a3f7-cd370c06d520)

Let test the browser again

![image](https://github.com/Matt4llan/Docker-AWS/assets/156334555/0c18cc3d-c1b4-4961-9f3f-820fa9a76658)

Success!!

Returning to the shell we can see that some web requests will have been logged corresponding to your browser requests:

![image](https://github.com/Matt4llan/Docker-AWS/assets/156334555/224978c7-cb54-48f3-a8f9-9d9ce15fd877)

There are two requests because the browser automatically requests a favicon in addition to the page content.






