# Docker on Linux for AWS

![image](https://github.com/Matt4llan/Docker-AWS/assets/156334555/de8a4a76-626b-49f7-9622-53f356bacb9c)

## Objective

The objective of this tutorial is to get up and running with Docker on Linux using an AWS virtual machine. I will work with images from the public Docker registry, run a handful of containers, and create your my image from which to create containers. 

### Skills Learned

By completing this lab, I will gain hands-on experience with installing Docker on Linux using an AWS virtual machine, Adding a user to the Docker group, finding and useing images from the public Docker Registry and building my own images using Dockerfiles

### Tools Used

- AWS - Amazon Cloud Hosting Platform
- Docker - To create the containers

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



