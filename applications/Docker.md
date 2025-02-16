Docker
======

Image manipulation
------------------

```bash
# Download prebuilt image
docker pull ubuntu:16.04

# See download images
docker images

# Build image from Dockerfile (need to disable command caching if using non-determinate commands like git or update)
docker build -t image:tag --no-cache=true .

# Remove image
docker rmi image:tag

# Remove all images
docker rmi $(docker images -q)

# Remove untagged images
docker rmi $(docker images --filter "dangling=true" -q --no-trunc) -f

# Use registry machine (on server)
docker run -d --name=registry --restart=always -p 5000:5000 -v /home/shook/docker_images:/var/lib/registry registry

# Use registry machine (on client or build machine)
docker build -t image_name:latest .
docker tag -f image_name:latest image_host:5000/image_name:latest
docker push image_host:5000/image_name:latest
docker pull image_host:5000/image_name:latest
```


Container manipulation
----------------------

```bash
# See container instances
docker ps -a

# Stop container
docker stop container-id

# Stop all containers
docker stop $(docker ps -a -q)

# Remove all stopped containers
docker rm $(docker ps --no-trunc -aq) -f

# Run a one-off command
docker run ubuntu:16.04 touch hi

# Run an interactive command
docker run -it ubuntu:16.04 python

# Open terminal/shell in running container
docker exec -it container-id bash

# Open a terminal/shell in a new container
docker run -it ubuntu:16.04 bash

# Open a long-running program in the foreground (ctrl+pq to detach, but leave running)
docker run -it ubuntu:16.04 bash -c "while true; do echo foo; sleep 5; done"

# Open a process in the background (include -it with the -d to enable input and ctrl+pq command)
docker run -dit ubuntu:16.04 bash -c "while true; do echo foo; sleep 5; done"

# Open a process, but use the host's network (useful for development if using localhost db connections inside the container)
docker run -it --net="host" ubuntu:16.04

# Follow logs
docker logs -f container-id

# See volumes
docker volume ls

# Remove all volumes
docker volume rm $(docker volume ls -q)
```


Small docker images
-------------------

When building docker images, if one of the stages of your build involves grabbing a ton of dependencies to build some binary/lib that aren't required afterward, you can use the "builder" pattern to only keep the binary (and not the rest of the dependencies:

```
FROM golang:1.7.3 as builder
WORKDIR /go/src/github.com/alexellis/href-counter/
RUN go get -d -v golang.org/x/net/html  
COPY app.go    .
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o app .

FROM alpine:latest  
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /go/src/github.com/alexellis/href-counter/app .
CMD ["./app"]
```


Sample docker-compose file
---------------------------

```
# Note: avoid using /home/shook on the right (docker) side of a volume mount, or it will populate your local directory with .rc files
version: '2'
services:
    dev:
        user: shook
        build:
            context: ../../
            dockerfile: ./dockerfiles/dev_env/Dockerfile
        image: dev
        links:
            - redis
            - mariadb
            - mongo
        ports:
            - 5000:5000
        volumes:
            - ../../:/home/shook/dev_repo
    redis:
        image: redis
    mariadb:
        image: mariadb
        environment:
            - MYSQL_ROOT_PASSWORD=pass
        volumes:
            - mariadbvolume:/var/lib/mysql
    mongo:
        image: mongo
        volumes:
            - mongovolume:/data/db
volumes:
    mongovolume:
    mariadbvolume:
```


Docker-compose commands
-----------------------

```bash
# Build a specific docker-compose file
docker-compose -f docker-compose.yml build

# Bring up all docker-compose services+dependencies
docker-compose -f docker-compose.yml up

# Bring up a particular service+dependencies (runs default cmd)
docker-compose -f docker-compose.yml up dev

# Run a bash script on a docker-compose file (services it depends on will be brought up in the background)
docker-compose -f docker-compose.yml run dev bash
```
