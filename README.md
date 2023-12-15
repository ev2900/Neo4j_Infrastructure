# Neo4j Infrastructure

<img width="275" alt="map-user" src="https://img.shields.io/badge/cloudformation template deployments-0-blue"> <img width="85" alt="map-user" src="https://img.shields.io/badge/views-510-green"> <img width="125" alt="map-user" src="https://img.shields.io/badge/unique visits-003-green">

This repository provides examples and reusable code sample with respect to deploying Neo4j Community Edition.

Different deployment options are explained. A list of the different options is below:
* [Neo4j via. docker without SSL](https://github.com/ev2900/Neo4j_Infrastructure/blob/main/README.md#without-ssl)
* [Neo4j via. docker with SSL via. self signed certificate](https://github.com/ev2900/Neo4j_Infrastructure/blob/main/README.md#with-self-signed-ssl-certificate)
* [Neo4j via. Amazon Elastic Container Service (ECS) Fargate without SSL](https://github.com/ev2900/Neo4j_Infrastructure/tree/main#without-ssl-1)

## Docker

[Install docker](https://docs.docker.com/engine/install/) on the machine you will be using to run these examples

If you are using Linux Ubuntu you can run the following commands to install both docker and docker compose

```sudo apt-get install docker``` <br>
```sudo apt-get install docker-compose```

## without SSL

Once you have docker installed to launch a docker container with Neo4j running in it, following the instructions below

1. Navigate to the [docker/without_ssl/](https://github.com/ev2900/Neo4j_Infrastructure/tree/main/docker/without_ssl) folder

2. Run ```docker-compose up```. Leave the terminal widow open

3. Go to [http://localhost:7474/browser/](http://localhost:7474/browser/) via. a web browser. The default user name and password are neo4j / neo4j

## with SSL via. self signed certificate

1. Generate a self signed certificate via. OpenSSL.

To enable SSL Neo4j requires a private key and a public certificate. To generate these with OpenSSL via. the terminal run the following commands to generate a private key and then use the private key to create a self signed certificate

```openssl genrsa -out private.key 2048```

```openssl req -new -x509 -key private.key -out public.crt -days 365```

2. Create the expected folder structure

Our [docker-compose.yaml](https://github.com/ev2900/Neo4j_Infrastructure/tree/main/docker/with_ssl_self-signed) file expects copies of the private.key and public.crt created in the previous steps to be arranged in a certain folder structure.

Arrange the [docker-compose.yaml](https://github.com/ev2900/Neo4j_Infrastructure/tree/main/docker/with_ssl_self-signed), copies of the private.key and public.crt so they matches the structure below. Create directories as needed

```
docker-compose.yaml
ssl/
    bolt/
        revoked/
        trusted/
            public.crt
        private.key
    https/
        revoked/
        trusted/
            public.crt
        private.key
```

3. Run ```docker-compose up```. Leave the terminal widow open

4. Go to [https://localhost:7473/browser/](https://localhost:7473/browser/) via. a web browser. The default user name and password are neo4j / neo4j

## AWS Elastic Container Service (ECS)

We can also host Neo4j on an AWS Fargate Elastic Container Service (ECS) instance. Follow the instructions below to deploy on ECS

## without SSL

1. Click the button below to deploy a CloudFormation stack

[![Launch CloudFormation Stack](https://sharkech-public.s3.amazonaws.com/misc-public/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home#/stacks/new?stackName=neo4j-without-ssl&templateURL=https://sharkech-public.s3.amazonaws.com/misc-public/neo4j_ecs_basic.yaml)

When you are deploying the stack toggle the publicly accessible option. If the option is set to Yes the CloudFormation stack will add a rule to the network security group to allow inbound network access from the public internet. If the option is set to No then post CloudFormation deployment you will need to update the inbound rules of the security group to allow traffic from your desired network location

<img width="650" alt="public_access_option" src="https://github.com/ev2900/Neo4j_Infrastructure/blob/main/README/public_access_option.png">

2. Locate the public ip address of the ECS task

Before we can navigate to the Neo4j browser we need to find the public IP address assigned to the ECS task. To find the public IP address navigate to the [neo4j-fargate-cluster](https://us-east-1.console.aws.amazon.com/ecs/v2/clusters/neo4j-fargate-cluster/) that was deployed by the CloudFormation template.

Then click on the task tab and then on the task id

<img width="650" alt="public_access_option" src="https://github.com/ev2900/Neo4j_Infrastructure/blob/main/README/ecs_task.PNG">

Locate the public IP address field

<img width="650" alt="public_access_option" src="https://github.com/ev2900/Neo4j_Infrastructure/blob/main/README/ecs_task_details.PNG">

3. Navigate to the Neo4j dashboard

Using the public IP address from the last step navigate to http://**elastic-ip**:7474/browser/

## with SSL via. self signed certificate

[![Launch CloudFormation Stack](https://sharkech-public.s3.amazonaws.com/misc-public/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home#/stacks/new?stackName=neo4j-with-ssl-self-signed&templateURL=https://sharkech-public.s3.amazonaws.com/misc-public/neo4j_ecs_ssl_self-signed.yaml)
