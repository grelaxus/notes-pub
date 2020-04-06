## Start container  
### from just pulled image (container doesn't exist yet):  

```sh
docker run --name qqq -d -it centos:6.9
```
__-d__ - background  
Command will create and start the container with name (in this example with name "qqq")  
With attached volume from the host OS:  
```sh
docker run -it --name tatata -v /home/tatata/docker-mount/:/usr/share/qqq centos:7
```
By default, [Docker containers are started with a reduced set of linux capabilities.](https://stackoverflow.com/questions/30547484/calling-openconnect-vpn-client-in-docker-container-shows-tunsetiff-failed-opera/48948987). To start a container with full network capabilities, either explicitly add the SYS_NET_ADMIN capability with __--cap-add__ argument e.g:  
```sh
docker run -d --cap-add SYS_NET_ADMIN myimage
```

Or give the container the full set of privileges with __--privileged__ e.g:  
```sh
docker run -it --name container-name -v /home/tatata/docker-mount/:/usr/share/qqq --privileged ubuntu-dev
```
Note: the use case for the 2 last commands is OpenConnect VPN, which cannot be started even as root, --priveleged helped

### To start existing container:  
```sh
docker start qqq
```

## Attaching to running container  
Don't use docker attach otherwise it will look like hang, see [here](https://stackoverflow.com/questions/35573698/why-does-docker-attach-hang)
Instead use [following](https://askubuntu.com/questions/505506/how-to-get-bash-or-ssh-into-a-running-container-in-background-mode) command:

```sh
$ docker exec -i -t 665b4a1e17b6 /bin/bash #by ID
```
or
```sh
$ docker exec -i -t loving_heisenberg /bin/sh #by Name
```

Attach to a container as root:  
```sh
docker exec -u 0 -it container_name  /bin/bash
```

## Useful for scripting

to remove a set of containers satisfying friendly_tho* pattern:  
```sh
docker rm $(docker container ls -a --format '{{.Names}}' | grep friendly_tho*)
```

to remove a set of images satisfying stuff_* pattern:  
```sh
docker rmi $(docker images | grep stuff_*)
```
see also [here](https://stackoverflow.com/questions/32490229/how-can-i-delete-docker-images-by-tag-preferably-with-wildcarding)

## Other useful commands

<details><summary>Multiple useful commands are listed here (expand)</summary><p>

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

 </p>
 </details>

# Images
## Export and Import images 

```sh
docker save -o <path for generated tar file> <image name>
```
```sh
docker load -i <path to image tar file>

```
e.g.
```sh
docker save -o build/my-operator.tar my-operator
```

# Dockerfile
In order to [minimize number of layers](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#minimize-the-number-of-layers) try to RUN as much commands in a single RUN as possible, using && and \ for splitting and next line

## Dockerfile Hints for Spring Boot Developers
Add Oracle Java to an Image
For CentOS/ RHEL

```sh
ENV JAVA_VERSION 8u31
ENV BUILD_VERSION b13
 
# Upgrading system
RUN yum -y upgrade && \
    yum -y install wget
 
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
Adding users

```sh
RUN useradd -ms /bin/bash newuser && \
        yum install git
USER newuser
WORKDIR /home/newuser
```
# DevOps
[setting up a private docker registry (official doc)](https://docs.docker.com/registry/deploying/#copy-an-image-from-docker-hub-to-your-registry)

[more detailed blog post on setting up a private docker registry](https://blog.sleeplessbeastie.eu/2018/04/16/how-to-setup-private-docker-registry/)


<details><summary>Here is how I did this to get it working over TLS (expand): </summary><p>


```sh
######################### Docker Registry ########################
#https://docs.docker.com/registry/insecure/
#https://docs.docker.com/registry/deploying/#get-a-certificate

# First creating a self-signed certificate
mkdir -p certs
openssl req \
  -newkey rsa:4096 -nodes -sha256 -keyout certs/domain.key \
  -x509 -days 365 -out certs/domain.crt
#in the prompt make sure to use the name myregistrydomain.com as a CN. IP-address may not work  
docker container stop registry

docker run -d --name registry   -v "$(pwd)"/certs:/certs   -e REGISTRY_HTTP_ADDR=0.0.0.0:443   -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt   -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key   -p 443:443   registry:2
docker logs --tail 50 --follow --timestamps registry

# Following command shows certficate info for the hostname/IP:  
openssl s_client -connect hostname/IP:PORT -showcerts


# makedir and copy certificate to client
# PS: don't change the hostname to IP - the certificate is generated for hostname. Instead add the hostname in /etc/hosts
sudo mkdir -p /etc/docker/certs.d/sc-cluster-05-02:443
sudo scp sc-cluster-05-02:/home/ztsadmin/certs/domain.crt /etc/docker/certs.d/sc-cluster-05-02:443/ca.crt

# push image to docker
docker tag image_name cluster-node-01:443/image_name:asdfqwer1234
docker push cluster-node-01:443/image_name:asdfqwer1234

# list images
curl --cacert /etc/docker/certs.d/cluster-node-01\:443/ca.crt -X GET https://cluster-node-01:443/v2/_catalog

curl --cacert /etc/docker/certs.d/cluster-node-01\:443/ca.crt -X GET https://cluster-node-01:443/v2/image_name/tags/list

# The gitpull-build-dockerpush can be scripted. For the build.sh and release.sh following article was useful: https://medium.com/travis-on-docker/how-to-version-your-docker-images-1d5c577ebf54
# Didn't use his image treeder/bump though. Used git current hash instead
# version=`git rev-parse --short HEAD`
# also not running git commands (pull, commit, push)
# he re-wrote the script in go https://github.com/treeder/dockers/tree/master/bump

# https://stackoverflow.com/questions/29802202/docker-registry-2-0-how-to-delete-unused-images
# https://medium.com/@mcvidanagama/cleanup-your-docker-registry-ef0527673e3a
sudo ls -al /var/lib/docker/volumes/7dae1eb510357ce0efb2219744b9115c1caac654affcb04340fbc938877eee0a/_data/docker/registry/v2/repositories/image_name/_layers/sha256
```

 </p>
 </details>

# Troubleshooting

If docker container runs in infinite loop of restarts (following [this](https://docs.docker.com/registry/insecure/) and [this](https://docs.docker.com/registry/deploying/#get-a-certificate) lead me to one) and nothing helps (container doesn't stop, it cannot be killed and reboot doesn't help), then follow the [advice](https://stackoverflow.com/questions/31365827/cannot-stop-or-restart-a-docker-container#comment88795495_48220556):
```sh
$ sudo systemctl restart docker.socket docker.service
$ docker rm <container id>
```
