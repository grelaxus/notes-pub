Docker attach 
Don't use docker attach otherwise it will look like hang, see [here](https://stackoverflow.com/questions/35573698/why-does-docker-attach-hang)
Instead use [following](https://askubuntu.com/questions/505506/how-to-get-bash-or-ssh-into-a-running-container-in-background-mode) command:

```sh
$ sudo docker exec -i -t 665b4a1e17b6 /bin/bash #by ID
```
or
```sh
$ sudo docker exec -i -t loving_heisenberg /bin/sh #by Name
```

List all Docker Images

```sh
docker images -a
```

List All Running Docker Containers

```sh
docker ps
```

List All Docker Containers

```sh
docker ps -a
```

Start a Docker Container

```sh
docker start <container name>
```

Stop a Docker Container

```sh
docker stop <container name>
```

Kill All Running Containers

```sh
docker kill $(docker ps -q)
```

View the logs of a Running Docker Container

```sh
docker logs <container name>
```

View details on the state, e.g. "OOMKilled" which is true if you exceed the container memory limits and Docker kills your app.

```sh
docker inspect $container_id
```
Start docker events (in the background) to see whats going on with a failing container.

```sh
docker events
```

OR in background (see details [here](https://serverfault.com/questions/596994/how-can-i-debug-a-docker-container-initialization))

```sh
docker events&
```

Delete All Stopped Docker Containers
Use -f option to nuke the running containers too.

```sh
docker rm $(docker ps -a -q)
```

Remove a Docker Image

```sh
docker rmi <image name>
```

Delete All Docker Images

```sh
docker rmi $(docker images -q)
```

Delete All Untagged (dangling) Docker Images

```sh
docker rmi $(docker images -q -f dangling=true)
```

Delete All Images

```sh
docker rmi $(docker images -q)
```

Remove Dangling Volumes

```sh
docker volume rm -f $(docker volume ls -f dangling=true -q)
```

SSH Into a Running Docker Container
Okay not technically SSH, but this will give you a bash shell in the container.

```sh
sudo docker exec -it <container name> bash
```

Use Docker Compose to Build Containers
Run from directory of your docker-compose.yml file.

```sh
docker-compose build
```

Use Docker Compose to Start a Group of Containers
Use this command from directory of your docker-compose.yml file.


```sh
docker-compose up -d
```
This will tell Docker to fetch the latest version of the container from the repo, and not use the local cache.


```sh
docker-compose up -d --force-recreate
```

This can be problematic if you’re doing CI builds with Jenkins and pushing Docker images to another host, or using for CI testing. I was deploying a Spring Boot Web Application from Jekins, and found the docker container was not getting refreshed with the latest Spring Boot artifact.

```sh
#stop docker containers, and rebuild
docker-compose stop -t 1
docker-compose rm -f
docker-compose pull
docker-compose build
docker-compose up -d
```

Follow the Logs of Running Docker Containers With Docker Compose

```sh
docker-compose logs -f
```

Save a Running Docker Container as an Image

```sh
docker commit <image name> <name for image>
```

Follow the logs of one container running under Docker Compose

```sh
docker-compose logs pump <name>
```

Dockerfile Hints for Spring Boot Developers
Add Oracle Java to an Image
For CentOS/ RHEL

```sh
ENV JAVA_VERSION 8u31
ENV BUILD_VERSION b13
 
# Upgrading system
RUN yum -y upgrade
RUN yum -y install wget
 
# Downloading & Config Java 8
RUN wget --no-cookies --no-check-certificate --header "Cookie: oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/$JAVA_VERSION-$BUILD_VERSION/jdk-$JAVA_VERSION-linux-x64.rpm" -O /tmp/jdk-8-linux-x64.rpm
RUN yum -y install /tmp/jdk-8-linux-x64.rpm
RUN alternatives --install /usr/bin/java jar /usr/java/latest/bin/java 200000
RUN alternatives --install /usr/bin/javaws javaws /usr/java/latest/bin/javaws 200000
RUN alternatives --install /usr/bin/javac javac /usr/java/latest/bin/javac 200000
```

Add / Run a Spring Boot Executable Jar to a Docker Image


```sh
VOLUME /tmp
ADD /maven/myapp-0.0.1-SNAPSHOT.jar myapp.jar
RUN sh -c 'touch /myapp.jar'
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/myapp.jar"]
```
