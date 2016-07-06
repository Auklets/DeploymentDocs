# DeploymentDocs

## Description
> This deployment documentation will enable you to run LoadEffect on your own servers for your load testing purposes.

## Table of Contents

1. [Server creation](#server-creation)
    1. [AWS](#AWS)
    2. [Digital Ocean](#digital-ocean)
1. [Shell connection](#shell-connection)
1. [Environment setup](#environment-setup)
    1. [Environment variables](#environment-variables)
    1. [Certificates](#certificates)
1. [Pull images](#pull-images)
1. [Run Containers](#run-containers)
    1. [MySQL Database](#mysql-database)
    2. [Web Application](#web-application)
1. [Monitor Containers](#monitor-containers)

## Server Creation

The first step for deployment is to create a docker-machine on a virtual private server provider of your choice. Here instructions and links for AWS and Digital Ocean.

### AWS
1. Configure AWS credentials, e.g.
![image](https://cloud.githubusercontent.com/assets/10008938/16574403/527dc472-4233-11e6-8048-40952573d602.png)
2. Run the docker-machine create command with the AWS driver:
```sh
docker-machine create --driver digitalocean --digitalocean-access-token="your-access-token" "your docker-machine name"
```
https://docs.docker.com/machine/drivers/aws/

### Digital Ocean
1. Generate your personal digital ocean access token:
![image](https://cloud.githubusercontent.com/assets/10008938/16574344/bf5f6150-4232-11e6-988d-2d1aa71c14f7.png)
2. Run the docker-machine create command with the digital ocean driver:
```sh
docker-machine create --driver digitalocean --digitalocean-access-token="your-access-token" "your docker-machine name"
```
Further details: https://docs.docker.com/machine/drivers/digital-ocean/

#### Other providers
Further instructions for other supported providers can be found here: https://docs.docker.com/machine/drivers/

## Shell connection

Get the eval command:
```sh
  docker-machine env "machine-name"
```

Run eval command to connect current shell to the docker-machine:
```sh
  # eval $(docker-machine env "machine-name")
```

## Environment setup

The environment setup on the docker-machine itself enables deployed docker containers to access sensitive data, which is not provided within the docker image.

Preparation: ssh into your docker-machine and create a folder called /env.
```sh
docker-machine ssh "machine-name"

mkdir /env
```

### Environment variables

1. Make a copy of the config.example.env file and name it production.env

2. Adjust all the necessary environment variables:

```sh
NODE_ENV=production
APP_NAME=lta
PROTOCOL=http://
JWT_SECRET=               -> your personal JWT_SECRET (any string)
WEB_HOST=                 -> the IP of your virtual private server
DOCKER_HOST=              -> the IP of your virtual private server
DB_HOST=                  -> the alias of your mysql database (suggested: mysql)
WEB_PORT=8000             -> the main application port: normally 8000
MASTER_PORT=2000          -> the master port: normally 2000
WORKER_PORT=5000          -> the worker port: normally 5000
DOCKER_PORT=2376          -> the docker port: normally 2376
DB_USER=root              -> your DB user, normally root
DB_PASSWORD=              -> your DB password, if used
```

3. Copy the production.env file on your docker-machine with the scp command:

```sh
  docker-machine scp "local-path-to-production.env-file" "machine-name":/env
```

### Certificates

After creating the docker-machine on a virtual private server the certificates for the connection are stored in the folloing directory (Mac):

```sh
  ~/.docker/machine/machines/"machine-name"
```
Those certificates have to be copied on to the docker-machine in the /env directory with the following command:

```sh
  docker-machine scp -r ~/.docker/machine/machines/"machine-name" "machine-name":/env

  specific example:
  docker-machine scp ~/.docker/machine/machines/aws01 aws01:/env
```

After that ssh into your docker-machine:
```sh
  docker-machine ssh "machine-name"
```

And rename the folder to certs:
```sh
  mv /env/aws01 /env/certs
```

#### Final check

Final step: check if the right production.env file is in your /env folder and the certificate files (especially ca.pem, certs.pem and key.pem) are in the /env/certs.

## Pull images

Run the following three commands to pull down the required images:
```sh
docker pull cshg/loadapp:production
docker pull cshg/loadmaster:production
docker pull cshg/loadworker:production
```
Hint: Redo the image pulls to get the latest versions at a later point in time.

## Run Containers

#### MySQL Database

Run MySQL Database container with the following command (make sure to use your own password):
```sh
  docker run --name mysql -e MYSQL_DATABASE=lta -e MYSQL_ROOT_PASSWORD="yourpassword" -d mysql/mysql-server
```

#### Web Application

Run the main application with the following command:
```sh
  docker run -d --name web -p 80:8000 -v /env:/env/ --link mysql cshg/loadapp:production
```

## Monitor Containers

These commands enable you to monitor if your docker containers run and interact correctly.

Basic command to check docker-machine status and shell connection:
```sh
  docker-machine ls
```

Basic command to check all containers and their run status:
```sh
  docker ps -a
```

Command to log container status and follow logs:
```sh
  docker logs -f "container-name or id"
```

Command to ssh into container:
```sh
  docker exec -it "container-name or id" /bin/bash
```

Command to check MySQL DB:
```sh
docker run -it --link mysql:mysql --rm mysql/mysql-server sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"'
```

##INTERNAL: Update images to latest production version

1. Pull down latest changes from master and production on to local fork:
```sh
git checkout master
git pull upstream master
git checkout production
git pull upstream production
```
2. Switch to local production branch and merge changes from master
```sh
git checkout production
git merge master
```
3. Push up changes from local production to upstream production
```sh
git push upstream production
```
4. Update images
```sh
docker pull cshg/loadapp:production
docker pull cshg/loadmaster:production
docker pull cshg/loadworker:production
```
