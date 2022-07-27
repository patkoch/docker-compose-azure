# Get started

This is about building containers and deploying them using Docker Compose on your local machine, respectively at Azure as a Container Instance.

For a more detailed manual please see the related blog post:

https://www.patrickkoch.dev/posts/post_20/

Prerequisites:
 - Docker Compose
 - Active Azure Subscription

## Building Docker Images with Dockerfiles

After cloning the repository, the containers can be builded by using the "docker build" command.

Start e.g.: a Windows PowerShell, change the directory to "first-linux-container" and run:

```
docker build -t first-linux-container . 
```

After that, change the directory to "second-linux-container" and run:

```
docker build -t second-linux-container . 
```

This will create the container images.

## Deploying the Containers on your local Machine

Change the directory to "docker-compose-yaml-files\local" and run following command:

```
docker compose up
```

This will run two containers, refering to the container images builded before. You can prove that by running:

```
docker ps
```

## Terminating the Containers on your local Machine

Run following command for terminating the containers:

```
docker compose down
```

## Deploying the Containers in Azure

In contrast to the previous approach, this aims to deploy the containers in Azure as Container Instance.
As prerequisite for that, you need an active Azure subscription.

### Login to Azure

```
az login
```

### Tag the Container Images properly 

Tag the container images, so they fit to be pushed to the Container Registry:

```
docker tag <Image ID> <Container Registry Name>.azurecr.io/containergroup:first-linux-container
```

```
docker tag <Image ID> <Container Registry Name>.azurecr.io/containergroup:second-linux-container
```

### Login to the Container Registry and push the Container Images

Use following command to login to your Container Registry, which will manage your Container Images:

```
az acr login --name <Container Registry Name>
```

Afterwards, push the Container Images using:

```
docker push <Container Registry Name>.azurecr.io/containergroup:first-linux-container
```

```
docker push <Container Registry Name>.azurecr.io/containergroup:second-linux-container
```
### Create and ACI Context and switch to the Context

Create a new ACI context by applying following command:

```
docker context create aci patricksacicontext
```

In addition, switch to that context:

```
docker context use patricksacicontext
```

### Deploying the Containers in Azure as Container Instance using Docker Compose

Adapt the Docker-Compose-YAML file, due to the name of the pushed Container Images, respectively the name of the Container Registry:

```
version: "3.9"
services:
  first-linux-container:
    image:  <Container Registry Name>.azurecr.io/containergroup:first-linux-container
  second-linux-container:
    image:  <Container Registry Name>.azurecr.io/containergroup:second-linux-container
```

Afterwards, apply the very same "docker compose" commands for deploying/terminating the containers:

```
docker compose up
```

```
docker compose down
```

The containers will be deployed as an "Azure Container Instance".