# Docker Notes

How to get Source code -> Container?

- volume that points to the source code
- Add your source code into a custom image that is used to create a container

Layered File System

![layered file system](./img/traditional_message_bad2.png)

Image layer is read only, but container is a read/write layer. If you get rid of container, you get rid of that read/write layer

## Containers and Volumes

Volume is special type of directoy in a container typically referred to as a "data volume"
Can be shared and reused among containers.
Updates to an image won't affect a data volume
Data volumes are persisted even after the container is deleted

Volume will write to the /mnt/ of the Docker host

Creating a Data Volume

```
$ docker run -p 8080:3000 -v /var/www node
$ docker inspce tmycontainer
//some info about the mount


//You can point container volume to a specific host directoy
$ docker run -p 8080:3000 -v $(pwd):/var/www node -w "start_directory" command_to_run
```

## Custom Images w/ Dockerfile

Key Dockerfile Instruction.

- FROM (Every dockerfile should start with FROM)
- MAINTAINER
- RUN
- COPY
- ENTRYPOINT
- WORKDIR
- EXPOSE
- ENV
- VOLUME

Ex.

``` Dockerfile
FROM node
MAINTAINER SEOK LEE
ENV NODE_ENV=production
COPY . /var/www
WORKDIR /var/www
RUN npm install
EXPOSE $PORT 
ENTRYPOINT ["node", "server.js"]
```

`` bash
//building custom image
$ docker build -t <username>/name_image ~/build_context/
// . is build context

$ docker push <username>/name_image
``

*NOTE* Be careful about defining volume in dockerfile.


## Container Linking Option

- Legacy Linking
- Adding Containers to a bridge network

### Legacy Linking

``` bash
//start container with a name
$ docker run -d --name my-postgres postgres

//my-postgres would be the name of linked-container, and postgres would be linked container alias
docker run -d -p 5000:5000 --link my-postgres:postgres lee/lee-java
```


### Container Networks

``` bash
//create a custom bridge network
docker network create --driver bridge isolated_network

//run containers in the networks.
docker run -d --net=isolated_network --name mongodb mongo

```

## Docker Compose

- Manages the whole application lifecycle:
    - Start, stop, and build services
    - View status of running services
    - Stream the log output of running services
    - run a one-off command on a service

Docker-compose.yml

``` yaml
version: '2'
services: 
    node:
        build:
            context: .
            dockerfile: node.dockerfile
        networks:
            -nodeapp-network
        ports:
            - "3000:3000"
    mongodb:
        image: mongo
        networks:
            - nodeapp-network
    
    networks:
        nodeapp-network:
            driver:bridge
```

Key service Config options

- build
- environment
- image
- ports
- volumes

```
docker-compose build
    - docker-compose build mongo (specifying)
docker-compose up
    - docker-compose up --no-deps node
docker-compose down
    - take all of the containers down
    - docker compose down --rmi all --volumes
docker-compose logs
docker-compose ps
docker-compose stop
docker-compose start
docker-compose rm
```

## Docker Cloud

- Link to different cloud providers
- setup and provision nodes
- create a stack of docker services to deploy in the cloud
- manage stacks and services

-----------------

# If you want to use a docker machien without using the default terminal

$ docker-machine env default
$ eval (docker-machine env default)


# Will run the command in docker service
docker-compose exec *service_name* _command_ 

docker-compose rm -f
docker rmi -f $(docker images -qf dangling=True)
docker pull
docker images

docker stop (docker ps -aq)
docker rm (docker ps -aq)
docker rmi $(docker ps -aq)

docker run -d / -it <image>
 - (-d) flag for detached
 - (-it) flag for interactive

docker swarm init --advertise-addr ip-addr:port --listen-adr ip-addr:port 

FYI engine port:2375
Secure Engine port : 2376
Swarm port : 2377

docker swarm join-token worker
docker swarm join-token manager


# pytest commands
py.test _location/of/tests_
py.test --cov-report term-missing --cov _location-of-project_
flake8 . --exclude __init__.py
