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
1. [Run Containers](#run-containers)
    1. [MySQL Database](#mysql-database)
    2. [Web Application](#web-application)
1. [Monitor Containers](#monitor-containers)

## Server Creation

The first step for deployment is to create a docker-machine on a virtual private server provider of your choice. Here instructions and links for AWS and Digital Ocean.

### AWS

### Digital Ocean

Further instructions for other supported providers can be found here: https://docs.docker.com/machine/drivers/

## Shell connection

Get command:
```sh
  docker-machine env "machine-name"
```

Run:
```sh
  # eval $(docker-machine env "machine-name")
```

## Environment setup

### Environment variables

### Certificates

## Run Containers

### MySQL Database

### Web Application

## Monitor Containers


