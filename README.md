# Neo4j Infrastructure

<img width="85" alt="map-user" src="https://img.shields.io/badge/views-048-green"> <img width="125" alt="map-user" src="https://img.shields.io/badge/unique visits-001-green">

This repository provides examples and reusable code sample with respect to deploying Neo4j Community Edition.

Different deployment options are explained. A list of the different options are below:
* [Neo4j via. docker without SSL](https://github.com/ev2900/Neo4j_Infrastructure/blob/main/README.md#without-ssl)
* [Neo4j via. docker with self signed SSL certificate](https://github.com/ev2900/Neo4j_Infrastructure/blob/main/README.md#with-self-signed-ssl-certificate)
* Neo4j via. Amazon Elastic Container Service (ECS) Fargate without SSL

## Docker

[Install docker](https://docs.docker.com/engine/install/) on the machine you will be using to run these examples

If you are using Linux Ubuntu you can run the following commands to install both docker and docker compose

1. ```sudo apt-get install docker``` <br>
2. ```sudo apt-get install docker-compose```

## without SSL

Once you have docker installed to launch a docker container with Neo4j running in it, following the instructions below 

1. Naviage to the [docker/without_ssl/](https://github.com/ev2900/Neo4j_Infrastructure/tree/main/docker/without_ssl) folder

2. Run ```docker-compose up```. Leave the terminal widow open

3. Naviage to [localhost:7474/browser/](http://localhost:7474/browser/). The default user name and password is neo4j / neo4j

## with self signed SSL certificate

2.

## AWS Elastic Container Service (ECS)

