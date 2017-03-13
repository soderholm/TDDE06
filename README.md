# Repo for Continious Integration in TDDE06 lab series

*Made by: Fredrik Håkansson (freha309) and Alexander Ulander (aleul850)*

## Docker commands:

#### GO Server
docker build -t heekzz/tdde06_go . (in mappen med Dockerfile för go app)

docker run  --name go_server --link pg_server:db -it --rm -d heekzz/tdde06_go

#### Database:
docker build -t heekzz/tdde06_postgres .

docker run --name pg_server --rm -d heekzz/tdde06_postgres

#### NGINX:
docker build -t heekzz/tdde06_nginx .

docker run --name nginx_server --rm --link go_server:app -d  -p 80:80 heekzz/tdde06_nginx


## How-To:
### Introduction
This is a tutorial of how to set up a Continious Integration platform which will be able to perform the following workflow:

1. A developer pushes to the GitLab repository.
2. As an answer to the new changes in the repository, Jenkins will pull the new code and build the application with Docker.
3. A Jenkins slave will build the system and run the tests.
4. The developers will be notified by the result of the test via email or Slack.

> ####Observate that this guide is written to be executed on a Linux or Unix system.

### Step 1: Installing
First of all you need to install Docker from [Docker's home page](http://www.docker.com). Pick a version that suits your system.

Install Jenkins from [Jenkins.io](https://jenkins.io/)

### Step 2: 