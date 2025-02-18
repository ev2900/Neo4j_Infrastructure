# Neo4j Infrastructure

<img width="275" alt="map-user" src="https://img.shields.io/badge/cloudformation template deployments-99-blue"> <img width="85" alt="map-user" src="https://img.shields.io/badge/views-708-green"> <img width="125" alt="map-user" src="https://img.shields.io/badge/unique visits-014-green">

This repository provides examples and reusable code sample with respect to deploying Neo4j Community Edition.

Different deployment options are explained. A list of the different options is below:
* [Neo4j via. docker without SSL](https://github.com/ev2900/Neo4j_Infrastructure/blob/main/README.md#without-ssl)
* [Neo4j via. docker with SSL via. self signed certificate](https://github.com/ev2900/Neo4j_Infrastructure/blob/main/README.md#with-self-signed-ssl-certificate)
* [Neo4j via. Amazon Elastic Container Service (ECS) Fargate without SSL](https://github.com/ev2900/Neo4j_Infrastructure/tree/main#without-ssl-1)
* [Neo4j via. Amazon Elastic Container Service (ECS) Fargate with SSL via. self signed certificate](https://github.com/ev2900/Neo4j_Infrastructure/tree/main#with-ssl-via-self-signed-certificate-1)

## Docker

[Install docker](https://docs.docker.com/engine/install/) on the machine you will be using to run these examples

If you are using Linux Ubuntu you can run the following commands to install both docker and docker compose

```sudo apt-get install docker``` <br>
```sudo apt-get install docker-compose```

### without SSL

Once you have docker installed to launch a docker container with Neo4j running in it, following the instructions below

1. Navigate to the [docker/without_ssl/](https://github.com/ev2900/Neo4j_Infrastructure/tree/main/docker/without_ssl) folder

2. Run ```docker-compose up```. Leave the terminal widow open

3. Go to [http://localhost:7474/browser/](http://localhost:7474/browser/) via. a web browser. The default user name and password are neo4j / neo4j

### with SSL via. self signed certificate

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
        public.crt
        private.key
    https/
        public.crt
        private.key
```

3. Run ```docker-compose up```. Leave the terminal widow open

4. Since we are using self signed certificate go to [https://localhost:7687/](https://localhost:7687/) and accept the security risk. You must allow the browser to ignore the security warning it throw for the self signed certificate

5. Go to [https://localhost:7473/browser/](https://localhost:7473/browser/) via. a web browser. The default user name and password are neo4j / neo4j

## AWS Elastic Container Service (ECS)

We can also host Neo4j on an AWS Fargate Elastic Container Service (ECS) instance. Follow the instructions below to deploy on ECS

### without SSL

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

Using the public IP address from the last step navigate to http://**elastic-ip**:7474/browser/ . The default username is *neo4j* and the default password is *neo4j*

### with SSL via. self signed certificate

1. Generate a self signed certificate via. OpenSSL.

To enable SSL Neo4j requires a private key and a public certificate. To generate these with OpenSSL via. the terminal run the following commands to generate a private key and then use the private key to create a self signed certificate

```openssl genrsa -out private.key 2048```

```openssl req -new -x509 -key private.key -out public.crt -days 365```

2. Create a custom container image that includes the SSL certificate and push the image to an AWS elastic container registry (ECR) repository

Copies of the SSL certificate file and private key file need to be on the local file system of the container under a specific directory. To make copies of the certificate and private key available, create a custom container image based on the public neo4j image but customized to include in the certificate and private key files. Once we create the customized container image, we will push it to AWS Elastic Container Registry. Follow the steps below

Click the button below to deploy a CloudFormation stack. The stack will create a repository the custom image can be pushed to

[![Launch CloudFormation Stack](https://sharkech-public.s3.amazonaws.com/misc-public/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home#/stacks/new?stackName=ecr-repository&templateURL=https://sharkech-public.s3.amazonaws.com/misc-public/ecr_repository.yaml)

Navigate to the [ECS repository home page](https://us-east-1.console.aws.amazon.com/ecr/private-registry/repositories). Click on the *neo4j-ecs-w-ssl* repository

<img width="700" alt="ecr_home_page" src="https://github.com/ev2900/Neo4j_Infrastructure/blob/main/README/ecr_home_page.png">

Then click on the view push commands button

<img width="700" alt="ecr_push_commands" src="https://github.com/ev2900/Neo4j_Infrastructure/blob/main/README/ecr_push_commands.png">

Follow the commands to build the image and push it to the ECR repository. When you are running the commands ensure your termainal is in a directory that has the [docker](https://github.com/ev2900/Neo4j_Infrastructure/blob/main/ecs/with_ssl_self-signed/dockerfile), certificate file and private key file.

3. Deploy Neo4j using on ECS using the customer container image

Click the button below to deploy a CloudFormation stack. The stack will deploy Neo4j to ECS along with all of the corresponding networking components.

[![Launch CloudFormation Stack](https://sharkech-public.s3.amazonaws.com/misc-public/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home#/stacks/new?stackName=neo4j-with-ssl-self-signed&templateURL=https://sharkech-public.s3.amazonaws.com/misc-public/neo4j_ecs_ssl_self-signed.yaml)

When you are deploying the stack toggle the publicly accessible option. If the option is set to Yes the CloudFormation stack will add a rule to the network security group to allow inbound network access from the public internet. If the option is set to No then post CloudFormation deployment you will need to update the inbound rules of the security group to allow traffic from your desired network location

<img width="650" alt="public_access_option_2" src="https://github.com/ev2900/Neo4j_Infrastructure/blob/main/README/public_accessable_2.png">

You will also need to enter the URI of the ECR image as part of the CloudFormation stack parmeters. This will be the URI of modified Neo4j image we built and loaded to ECR in step 2.

<img width="650" alt="public_access_option_2" src="https://github.com/ev2900/Neo4j_Infrastructure/blob/main/README/image_URI.png">

To find the URI of the container image, navigate to the [ECS repository home page](https://us-east-1.console.aws.amazon.com/ecr/private-registry/repositories). Click on the *neo4j-ecs-w-ssl* repository and click on the copy URI button

<img width="650" alt="public_access_option_2" src="https://github.com/ev2900/Neo4j_Infrastructure/blob/main/README/copy_URI.png">

4. Access Neo4j via. the Neo4j dashboard

Before we can navigate to the Neo4j browser we need to find the public IP address assigned to the ECS task. To find the public IP address navigate to the [neo4j-fargate-cluster](https://us-east-1.console.aws.amazon.com/ecs/v2/clusters/neo4j-fargate-cluster/) that was deployed by the CloudFormation template.

Then click on the task tab and then on the task id

<img width="650" alt="public_access_option" src="https://github.com/ev2900/Neo4j_Infrastructure/blob/main/README/ecs_task.PNG">

Locate the public IP address field

<img width="650" alt="public_access_option" src="https://github.com/ev2900/Neo4j_Infrastructure/blob/main/README/ecs_task_details.PNG">

Since we are using self signed certificate go to https://**elastic-ip**:7687 and accept the security risk. You must allow the browser to ignore the security warning it throw for the self signed certificate

Then navigate to https://**elastic-ip**:7473/browser/ . The default username is *neo4j* and the default password is *neo4j*
