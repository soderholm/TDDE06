# Repo for Continious Integration in TDDE06 lab series

*Made by: Fredrik Håkansson (freha309) and Alexander Ulander (aleul850)*

## Scenario:
Imagine that you have a large scale software project with multiple developers where when everytime you make changes, you have to manually go through the whole tedious cycle of e.g.:
1. uploading the new code to github
2. getting the new code to your server 
3. testing it with the new code 
4. updating the server with the new code if the tests are successfull
5. restarting the servers
6. notifying the other developers about the changes 

Now imagine that instead of doing all of these steps after every push, you have a system that triggers on your push and performs all of your desired code integration steps automatically. Well, now you can. Introducing continous integration (CI) - with Docker and Jenkins.

## How-To:
### Introduction
This is a tutorial of how to set up a Continious Integration platform which will be able to perform the following workflow:

1. A developer pushes to the GitHub repository.
2. As an answer to the new changes in the repository, Jenkins will pull the new code and build the application with Docker.
3. A Jenkins slave will build the system and run the tests.
4. The developers will be notified by the result of the test via email or Slack.

> #### Observate that this guide is written to be executed on a Linux or Unix system.

The project we will work with is the [example project for the course](https://gitlab.ida.liu.se/large-scale-dev/ci-sample-project) which you can read more about via the link.
Shortly it is a very simple web server written in Golang with a PostgreSQL database and a NGINX server proxy.

### Step 1: Installing

#### Docker
First of all you need to install Docker from [Docker's home page](http://www.docker.com). Pick a version that suits your system.

- Docker training videos: https://training.docker.com/self-paced-training
- Getting started:
	- Linux: https://docs.docker.com/linux/
	- OS X: https://docs.docker.com/mac/
	- Windows: https://docs.docker.com/windows/
- Official documentation: https://docs.docker.com

In the course TDDE06 you can use a server for running Docker provided by IDA on address `130.236.181.180`. More information [here](http://www.ida.liu.se/~TDDE06/labs/ci-lab1.shtml)

#### Jenkins
Install Jenkins from [Jenkins.io](https://jenkins.io/). Install it on a machine that is reachable over the internet (not only on your `localhost`). It is possible to run Jenkins in a Docker container on the same server provied above or on a server you deploy. We will configure Jenkins in a later step so just make sure you can run Jenkins and access it's web interface for now.

### Step 2: Pull Docker images
Now we are going to get our hands on with Docker. We recommend to read the official documentation to get an understanding on the basic idea of Docker.

During Lab 1 we created some [Docker repositories](https://hub.docker.com/search/?isAutomated=0&isOfficial=0&page=1&pullCount=0&q=heekzz&starCount=0) that you can reach on Docker hub. 
We have one docker image per component and each Dockerfile can be found in the corresponding folder in this GitHub repository.

By running 
```
docker pull heekzz/tdde06_go
``` 
we will pull the Docker image for the Go server. By running 
```
docker pull heekzz/tdde06_postgres
``` 
we will pull the image for the postgres database. And then repeat the same with the NGINX proxy.

We now have our Docker images configurated and ready to run.

### Step 3: Running Docker containers

We can now run the Docker images we just pulled in a container. We need to run the three images in the order 
```javascript
postgres -> Go server -> NGINX
```
since the server is depentent on the database. A Docker container is created when we run an image with the command `docker run`*`<image>`*. But we will add some flags to our `run` command which in the end will look like this for the postgres server
```
docker run --name pg_server -d heekzz/tdde06_postgres
```
, the go server
```
docker run --name go_server --link pg_server:db -it -d heekzz/tdde06_go
```
and finally the NGINX server
```
docker run --name nginx_server -d --link go_server:app -p 80:80 heekzz/tdde06_nginx
```
Short explanation of the flags we used:

- **-d:**  detatch, run the container in background when launching from terminal.
- **--name:** names the container to a custom name.
- **--link:** creates a link between the two containers. --link pg_server:db enables us to use the address and port of the postgres container as environment variables in the Go container.

Now you should be able to connect to the server on `localhost:80` on the machine running the Docker containers. We will use the machine provided for the course further in the instructions when we start to interact with Jenkins.

This is just a test of things...

### Step 4: Configure Jenkins
*TODO: Add jenkins tutorial*

### Step 5: Add webhooks from GitHub
Now you want to set up the trigger of your CI, to be when you push changes to your GitHub project. To set this up, go to your desired GitHub repository's settings. Now choose "Integrations & services", press "Add service", search for "jenkins github" and select the only search result that pops up. Now add the Jenkins hook url, which will be the url for where your jenkins master is located (along with an optional port number, e.g. myserver.com:8080) and finally add the service.
