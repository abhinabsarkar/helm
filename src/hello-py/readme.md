# Hello world dockerized python app
This is a hello world Python application.

## Build the docker image and run container from the local image
```cmd
# Build docker image with the tag alpine3.7
# Syntax
docker build --tag <image_name> .
# Example
docker build --tag abs-hello-py:alpine3.7 .
# bind port 5000 of the container to port 9000 on 127.0.0.1 of the host machine
# Syntax
docker run --name <container_name> -p <host_port>:<container_port> <image_name>
# Example
docker run --name hello-py -p 9000:5000 abs-hello-py
```

## Push the docker image to remote(docker hub) repository
```cmd
# Login to docker repository
docker login
# Map local image to repository name
# Syntax
docker tag local-image:tagname reponame:tagname
# Example
docker tag abs-hello-py:alpine3.7 abhinabsarkar/abs-hello-py:alpine3.7
# Push local image to the target repo
# Syntax
docker push reponame:tagname
# Example
docker push abhinabsarkar/abs-hello-py:alpine3.7
```

## Run the container by downloading the image from the docker (remote) repo
```cmd
# Pull the docker image
docker pull abhinabsarkar/abs-hello-py:alpine3.7
# Run the container from the image pulled
# Syntax
docker run --name <container_name> -p <host_port>:<container_port> <image_name>
# Example
docker run --name hello-py -p 9000:5000 abhinabsarkar/abs-hello-py:alpine3.7
```