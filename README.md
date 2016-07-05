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

### Environment variables

### Certificates

## Pull images

Run the following three commands to pull down the required images:
```sh
docker pull cshg/loadapp:production
docker pull cshg/loadmaster:production
docker pull cshg/loadworker:production
```

## Run Containers

### MySQL Database

Run MySQL Database container with the following command (make sure to use your own password):
```sh
  docker run --name mysql -e MYSQL_DATABASE=lta -e MYSQL_ROOT_PASSWORD="yourpassword" -d mysql/mysql-server
```

### Web Application

Run the main application with the following command:
```sh
  dk run -d --name web -p 80:8000 -v /env:/env/ --link mysql cshg/loadapp:production
```

## Monitor Containers


