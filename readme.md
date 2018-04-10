# Udemy Course: Docker Mastery. The Complete Toolset From a Docker Captain
[Course](https://www.udemy.com/docker-mastery/learn/v4/overview)
[Github Repo](https://github.com/bretfisher/udemy-docker-mastery)

## Section 1 - Course Intro and DDocker Setup

### Lecture 5 - Docker Editions: Which Do I Use?

* there are 12+ editions
* there is Docker CE (community edition) and EE (enterprise edition)
* stable, edge releases
* docker is no longer one-click installation
* containers change fast. version matters, stay to latest version stick with it
* there are 3 major types of install: Direct, Mac/Win, Cloud
	* direct: you install it on a os and kernel and it runs (native support), linux, raspberry, windows srv 2016
	* win/mac: suite of tools, gui to make linux containers or win containers. mac does not support it natively. in Mac and Win10 a VM is started to run containers
	* cloud: AWS. Google Cloud, MS Azure. usually docker on linux and vendor specific features. AWS=>cloud formation temple, persistent storage, elastic load balancers
* All options support full lifecycle of docker options
* EE = support + Extra products for large organizations (LDAP authentication multiserver gui)
* go for stable (1 every 3month) 4month support

### Lecture 6 - Installing Docker the Fast Way

* for linux: Donot use the build in default packages apt install docker.io
* use the docker automated script to add their repo and add dependencies `curl -sSL https://get.docker.com/ | sh` [ubuntu setup](https://store.docker.com/editions/community/docker-ce-server-ubuntu)
* for windows server (development env? docker for win) (production? docker container) . LInux VM docker on it

### Lecture 11 - Docker for Linux Setup and Tips

* install on linux locally, on a linux server in cloud or in a linux vm
* the easiest way to get started on docker. 
* was made for linux (why?!?)
* there is no emulation, or vm, it runs straight on the operating system
* 3 ways to get docker on linux
	* script: get and run docker.com script (latest edge release, not for production systems) `curl -sSL https://get.docker.com/ | sh`
	* store: store.docker.com has instructions for each distro (RedHatEL) officially only supports Docker EE (paid) buth CentOS version will work
	* docker-machine: install in a VM, cloud instance, all are the same process (may no work for unlisted distros eg amazon linux, linod linux)
* Dont Use pre-installed setupe (Digital Ocean, Linode etc) because you get an older version and Docker is changing fast
* Use the official Fedora, Ubunut or CentOS installs and install them manually
* Installation Steps
	* Install docker (update package manager  and install docker)
	* add our user to docker group (to avoid using sudo every time)
	* clone teachers github repo
	* tweak terminal and shell (optional)
* if we install docker with aptitude package manager we get an old version
* we got to `https://docs.docker.com/install/linux/docker-ce/ubuntu/`
* this is the lts release. we want the edge to get lateset feats
* we go to `https://get.docker.com/` it has a script whch we can use with curl to install
* we run first command `curl -fsSL get.docker.com -o get-docker.sh` at a folder where we want our tools to reside
* we run the downloaded script `sh get-docker.sh`
* log suggest us to add our user to the docker group `sudo usermod -aG docker <username>`
* we check installation of docker with `sudo docker version`
* docker requires root to use core linux functionality. when we add a user to the docker group we give him root permissions. on redhat we must be root or use sudo everytime we run a docker command for security reasons
* if we run just `docker version` we get permission denial
* we need wo more tools (docker machine & docker compose). in linux version they are separate in windows and mac version they are bundled
* docker machine install 

```
curl -L https://github.com/docker/machine/releases/download/v0.14.0/docker-machine-`uname -s`-`uname -m` >/tmp/docker-machine && \
sudo install /tmp/docker-machine /usr/local/bin/docker-machine
```

* check docker machine install 	`docker-machine version`
* for docker compose we go to `https://github.com/docker/compose/releases`
* we get the script and run it

```
curl -L https://github.com/docker/compose/releases/download/1.20.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

* we get permissions issues sowe write `sudo -i` and cp the same script again
* we write `exit` in sudo console
* we run `docker-compose version` to check installation
* because compose and machine are manually installed they have to be manually updated. so we need to put them in our update flow
* clone the github repo
* run more nodes: docker-machine create --driver [vm](https://docs.docker.com/machine/drivers/)

## Section 2- Creating and Using Containers

* check versions of our docker cli and engine
* create a Nginx (web server) container
* Learn  common container management commands
* Learn Docker networking basics

### Lecture 15 - Check our Docker Install & Config

* use 'docker version'. i et permission issues so use 'sudo ...'
* client and server/engine should but dont have to be same version
* `sudo docker info` returns more info about the docker engine
* `docker` returns the available commands, management commands and normal commands
* new management commands formal `docker <command> <subcommand> (options)`
* old way(still works): `docker <command> (options)`
* example-new: `docker container run` example-old: `docker run`

### Lecture 16 - Starting an Nginx web server

* Course objectives: image vs container, run/stop/remove containers, check container logs and processes
* Image is the application we want to run. binaries,libs,code that make our application
* a container is a running instance of that image running as a process
* we can have many containers running off the same image
* for this lecture our image will be the nginx web server
* Docker Hub is the default image *registry* like what npm is for js modules or github for opensource projects
* we create anew container with `docker container run --publish 80:80 nginx`

* i visit `http://localhost` and nginx is running on our mchine. if i refresh i see the server log on the terminal console
* what just happened is
	* docker donwloaded image *nginx* from Docker Hub
	* started a new container from that image
	* opened port 80 on the host IP
	* routes that taffic to the container IP:port 80
* if the port is used by an app or another container we get bind error then we can use another port e.g 8080 using 8080:80 instead as it has to be routed to the containers port 80 (default web port)
* ctrl+c sends a stop signalto the process when running to the foreground. if i visit localhost now is down. container has stopped
* `docker container run --publish 80:80 --detach nginx` tell docker to run the container in the background. witout a terminal rnning i can visit localhost with chrome and see nginx running
* to list my running containers: `docker container ls`
* to stop the conteiner i type: `docker container stop <id>` i can type the first digits of id as long as they are unique. if i list them it is gone. 
* ls command lists only running containers, ls -a listes all even stopped. what i see is that when i run a contianer in the foreground of the rterminal and ctrl-c it is is stopped
* whenever i type contianer run docker starts a NEW container
* also i see that the container name is random. if i want to give aspecific name iuse the option --name <myname>. e.g `docker container run --publish 80:80 --detach --name webhost nginx`
* to see the logs of a detached container I run `docker container logs webhost` (returns last entries)
* `docker container top <coantenrname>` we see the process running INSIDE the container (nginx has a master process spawing worker processes)
* to remove all containers at once i use `docker container rm <id1> <id2> <id3>` this does not remove running contianers. to avoid stopping and then removeing i can use the -f (force) flag `docker container rm -f ...`

### Lecture 17 - What happens When we Run a Container

* docker looks for the image we specify in the end of the command locally in image cache.
* if it does not find it there it looks in a remote image repo )default is docker hub
* it downloads the latest version, we can specify the image version with a : image:latest is the default if we omit it.
* creates a NEW container based on that image and prepares to start
* gives it a virtual IP on a private network inside the docker engine (in our example is 80 the second part of the --publish hostPort:VirtualPort pair)
* opens up  port on host and forwards to the conainer port (the first part of the publish pair)
* if we dont define ports with publish no ports will be opened
* starts the container using the CMD in the image Dockerfile
* example of changing the defaults

`docker container run --publish 8080:80 --name webhost -d nginx:1.11 nginx -T` here we set a port on host, give the container a name, set a specific version of image and after theimage name:version we change the CMD on starting the container, -d is shorthand for --detach.

### Lecture 18 -  Container VS VM: its just a process

* [docker ebook](https://github.com/mikegcoleman/docker101/blob/master/Docker_eBook_Jan_2017.pdf)
* [docker for mac](https://www.bretfisher.com/docker-for-mac-commands-for-getting-into-local-docker-vm/)
* [docker win](https://www.bretfisher.com/getting-a-shell-in-the-docker-for-windows-vm/)
* [containers internals](https://www.youtube.com/watch?v=sK5i-N34im8&feature=youtu.be&list=PLBmVKD7o3L8v7Kl_XXh3KaJl9Qw2lyuFl)
* many people compare contianers to Vs, this is wrong
* they arent mini VMs. they are just processes, limited to what resources they can access, exit when proces stops
* to see it in action we start a mongodb container `docker run --name mongo -d mongo`. we see the containers list with `docker ps` and see the running processes in teh container with `docker top mongo`. it is just one process. if i  list the host processes with `px aux` we will see a process with name mongod and same pid like the single mongo container process

### Lecture 19 - Windows Containers: Docker is no longer just Linux

* brand new.
* winbdows containers are native .exe binaries running in docker conatainers on a windows kkernel. no linux
* [win containers and docker](https://www.youtube.com/watch?v=066-9yw8-7c)
* [docker microsoft](https://www.youtube.com/watch?v=QASAqcuuzgI)

### Lecture 20 - Assignemnt: Manage multiple Containers

* help: docs.docker.com and --help
* we start a 3 container application: nginx,  mysql, httpd(apached) srv
* we run tem in --detach mode and name them
* nginx should listen to 8080:80, httpd 8080:80 mysql 3306:3306
* when running mysql we have to use the --env option (-e) to pass MYSQL_RANDOM_ROOT_PASSWORD=yes
* use docker container logs on mysql to find the random password it created on startup
clean up with `docker container stop` and `docker container rm`
* check they are deleted before after cleanup

```
sudo docker container run --publish 80:80 -d --name webhost nginx
sudo docker container run --publish 8080:80 -d --name appached httpd
sudo docker container run --publish 3306:3306 -d --name sql -e MYSQL_RANDOM_ROOT_PASSWORD=yes mysql
sudo docker container ls -a
sudo dockers container logs sql

//root pswd oojo5paithieFeu5Iuke4IeZahzoj7Re

sudo docker container stop appached sql webhost mongo
sudo docker rm <id> 
sudo docker container ls -a
```

* docker image ls???

### Lecture 22 - Whats going on in Containers

* we can use various commands to look inside a container
* `docker container top` = process list in teh container
* `docker container inspect` = details of one container config
* `docker container stats` = performance stats for all containers
* we create two containers to see it in practice

```
sudo docker container run -d --name nginx nginx
sudo docker run -d --name mysql -e MYSQL_RANDOM_ROOT_PASSWORD=true mysql
```

* we see the running gcontainer list with `sudo docker container ls`
* we see the mysql container process list qith `sudo docker container top mysql` +> only one proc, we do the same for nginx=> 2 procs
* we use `sudo docker container inspect mysql` => we get a JSON config File
* we want to see live stats of containers `sudo docker container stats`

### Lecture 23 - Getting a Shell Inside the Container

* [package management](https://www.digitalocean.com/community/tutorials/package-management-basics-apt-yum-dnf-pkg)
* to start a container interactively `docker container run -it`
* to enter a running container and launch a second process (additional command inside) we can also use `docker container exec -it`
* -t gives a pseudoterminal , -i keep session open to receive input
* `sudo docker container run -it --name proxy nginx bash` bash is a command passed to the container as argument. thsi command logs me in as root in the container. i see i can type linux shell commands. it is actually a linux installation with a filesystem. i can install packages and do linux admin stuff
* to exit the shell i type `exit`. now container is stopped
* the default in containers is to run the image program. we changed that to run bash so that we can start it internally
* containers run as long as the command in startup script runs
* i can even run a ful ubuntu container with `sudo docker container run -it --name ubuntu ubuntu`. inside i can use apt package manager `apt-get update` and all
* ubuntu docker image is minimal. we have to install everything manually
* to restart a container with shell enabled i write `sudo docker container start -ai ubuntu`
* to enter a running container and launch a terminal i use `sudo docker contianer exec -it nginx bash`
* if i then issue ps aux from the terminal inside of the container i see an extra process running for bash
* if i exit the terminal after the exec command contiane ris still running
* alpine linux is a small (5MB) security focused linux distro
* we can pull images from docker hub to our local repo with `sudo docker pull <image>`. we can see our repo with `sudo docker image ls`
* we run an alpine container with bash terminal 	`sudo docker container run -it alpine bash`. bash is not installed in alpine. we can start only what is installed. we use sh onstead to get inside
* alpine package manager is apk

### Lecture 24 - Docker Networks: Concepts for Private and Public Comms in Containers

* [docker --format](https://docs.docker.com/config/formatting/)
* `sudo docker container run -p` sets the port of the running container used on host machine
* we can see the port used by a container with `sudo docker contianer port <container>`
* each container connects to a private virtual networ *bridge*
* each virtual network routes through a NAT firewall on host IP
* all containers on a virtual network can talk to each other without setting -p (exposed to the host)
* best practice is to to create a virtual private network for each app and let the connected servers (images-containers) talk to each other in private. e.g network *my_api* for mongo and nodejs containers, network my_web_app for mysql and apache/php containers
* Docker defaults work well , but are easy to swap or customize.
* we can make virtual networks and attach container to more than one virtual network or none
* if ye want to skip virtual networks and use host IP `--net=host`
* we can use docker network drivers to get new abilities

```
sudo docker container run -p 80:80 --name webhost -d nginx
sudo docker container port webhost
>>> 80/tcp -> 0.0.0.0:80
```

* we use inspect with --format option to grep though the config file of the running container for its virtual network ip

```
sudo docker container inspect --format '{{ .NetworkSettings.IPAddress }}' webhost
>>> 172.17.0.2

* running containers connect to a virtua network (bridgE or dockerO) through thir ports. virtual netwokr does to the routing to the host. host by default has a edge firewall to the external worls opening ports on demant -p (--publish) does that opens a port on host firewall and routes it through the docker bridge to the virtual nw ip and port
```

### Lecture 26 - Docker Networks: CLI Management of Virtual Networks

* to list all  networks on machine `sudo docker network ls`
* to inspect a network confog file `sudo docker network inspect <networkid or name>`
* to create anetwork `sudo docker network create --driver`
* attach/detach a container to a network `sudo docker network connect/disconnect`
* beidge is the default docker virtual network
* if we inspect it in the config file we see all teh contianer attached to it, ips etc also the gatweay
* host network is a special network that attaches virtual networks to host
* none is a network with nothing attached.not even a driver
* we createa virtual network `sudo docker network create my_app_net` and list it and inspect it. it uses bridge driver and a different subnet
* several options available when creating a nw
* we can use --network flag to specify the network for our new contianer to be attached `sudo docker container run -d --name new_nginx --network my_app_net nginx`
* we can connect a running container to a nw. `sudo docker network connect <networkid> <containerid>`. if the container is connected to a network now it is connected to two networks
* docker offers security. all ports closed by default . we manually expese what we want with -p

### Lecture 27 - DNS and how Containers Find Each Other

* DNS is the key to ease inter-contianer ccomms. ips are dynamic
* --link option enables DNS on default bridge network
* Docker uses DNS naming and has abuilt in DNS server. container names are the host names
* i add a secont container in my_app_net. i inspect the network config to verfiy that. the two contianers can communicate to each other with their names through DNS
* ican ping each other (my images should be nginx:alpine though) with `sudo docker container exec -it my_nginx ping new_nginx`
* the default BRIDGE network does not have abuilt in DNS server so when we run containers we have to use --link and a list of container names in [sont1, cont2] that he can talk to. in networks we create DNS is serve ris built in so no needfor --list

### Lecture 28 - Assignement: Use containers for cli testing

* use different linux distro to test curl cli tool version
* we use different terminal windows to start bash on cenos:7 and ubuntu:14.04 using -it. docker container -rm to clean up af5ter
* ensure culr is installed and updated on latest version. ubuntu: apt-get update && apt-get install curl, centos: yum update curl
* check unstall: curl --version
* DONE
* Seems like if i add --rm when I run a contianer it gets erased after stoping

### Lecture 30 - Assignemnt DNS round-robin test

* basic dns commands (alias)
* round robin is the concept of multiple servers licening to the same dns name
* since docker engine 1.11 we can have multiple containers on a CREATED network respond to the same DNS addres using alias
* we create a new virtual network (default driver) , create 2 contianers from ealsticsearch:2 image
* we use the *--net-alias search* when creating them to give them an additional DNS name to repsond to
* By default i cannot run containers with the same name. alias solves it
* we run alpine nslookup search with --net to see the two containers list for the same DNS name
* run centos curl -s search:9200 with --net multiple times to see both names fields show
* round robin is not a true load balancer
* we create the new virtual network `sudo docker network create my_net`
* we create 1st container add it to nwetworka nd set an alias `sudo docker container run -d --name search1 --network my_net --net-alias search elasticsearch:2`
* we create 2nd container add it to nwetworka nd set an alias `sudo docker container run -d --name search2 --network my_net --net-alias search elasticsearch:2`
* we create alpine container `sudo docker container run --rm -it --name alpine --network my_net alpine sh`
* we create centos contianer `sudo docker container run -it --rm --name centos --network my_net centos:7 bash
`
* we run nslookup in alpine and get 

```
Name:      search
Address 1: 172.18.0.3 search2.my_net
Address 2: 172.18.0.2 search1.my_net
```

* we run curl in centosand get different server uuids `sudo docker container run --rm --net my_net centos:7 curl -s search:9200`

## Section 3 - Container Images, Where to Find Them and How to Build them


### Lecture 32 - What is an Image 

* [image specs](https://github.com/moby/moby/blob/master/image/spec/v1.md)
* is the app binaries and dependencies and metadata of how to run it
* official def *an ordered collection of root filesystem changes and the corresponding execution parameters for use within a container runtime*
* not a complete OS. no kerner or kernel modules only binaries. the host provides the kernel
* small as even one file. (app binary) like a golang static bin
* big as alinux distro with modules preinstalled

### Lecture 33 - The Mighty Hub: Using Docker Hub Registry Images

* [official image lib](https://github.com/docker-library/official-images/tree/master/library)
* lecture objectives: basics of docker hub, oficial and non-oficial public images, downloading images, image tags basics
* we go to dockerhu and search for nginx we get 32k results!!! we go for the official.
* officials use just the name no slashes. what users create have their username and then a slash and then the image name
* officials are maintined by docker, well documented, versioned
* images are tagged and we can use the tags when adding them to our host repo. default tag (when we don specify it with : is the latest)
* we download images to our repo with `sudo docker pull <image>` (latest version) or `sudo docker pull <image>:<tag>` specific version
* good practice for production to specify exct version
* for nginx there is an alpine version with smaller footprint
* in our local repo we can see the image id to see if images are just different versions of same file (so size does nbot add up)
* docker hub is like github
* explore lists official images

### Lecture 34 - Images and their Layers:Discover the Image Cache

* [images & containers](https://docs.docker.com/storage/storagedriver/)
* lecture objectives: union file syustem, history,inspect commands, copy on write
* images are made using the union file system aking layers on changes
* `sudo docker history <image>:<tag>` gives a list

```
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
7f70b30f2cc6        2 weeks ago         /bin/sh -c #(nop)  CMD ["nginx" "-g" "daemon…   0B                  
<missing>           2 weeks ago         /bin/sh -c #(nop)  STOPSIGNAL [SIGTERM]         0B                  
<missing>           2 weeks ago         /bin/sh -c #(nop)  EXPOSE 80/tcp                0B                  
<missing>           2 weeks ago         /bin/sh -c ln -sf /dev/stdout /var/log/nginx…   22B                 
<missing>           2 weeks ago         /bin/sh -c set -x  && apt-get update  && apt…   53.4MB              
<missing>           2 weeks ago         /bin/sh -c #(nop)  ENV NJS_VERSION=1.13.10.0…   0B                  
<missing>           2 weeks ago         /bin/sh -c #(nop)  ENV NGINX_VERSION=1.13.10…   0B                  
<missing>           3 weeks ago         /bin/sh -c #(nop)  LABEL maintainer=NGINX Do…   0B                  
<missing>           3 weeks ago         /bin/sh -c #(nop)  CMD ["bash"]                 0B                  
<missing>           3 weeks ago         /bin/sh -c #(nop) ADD file:e3250bb9848f956bd…   55.3MB   
```

* this is a list of the image layers. each image starts as a bare empty layer (scratch) and each change creates a new layer
* any change in dockerfile or the use of docker commit command creates a new layer
* diff images can share layers using the image cache. this saves time and space
* so each image is only stored once. shared layers are not reinstalled. 
* when we have an apache image and make a container out of it. docker justs adds one more layer for read/write for each container reusing the image thus saving resources.
* when a container changes or writes to a file in the image this file is copied to the container layer file so the image remains intact for other containers (Copy on Write)
* `sudo docker inspect <image>:<tag>` shows the image metadata. all sorts of data are included.

### Lecture 35 - Image Tagging and Pushing to DockerHub

* lecture objectives: image tags and how to upload them to hub, image id vs Tag
* tagging. images have no name appart from the id (hash) they are identified by <user>/<repo>:<tag>, only official ones have no user
* if i use different tags of the same image i see they have the same image id (is the same image)
* to add tags we can create our own dockerfiles or tag existing images
`sudo docker image tag nginx <username>/nginx` i write the image i want  to tag first and then the new tag
* i see the addition to the image list but the id is the same
* i try to push it to the hub with `sudo docker push <username>/nginx`
* i get a denial as i am logedin. login `sudo docker login`
* i try again and success. i push to dockehub. i can push oly to my username/image or organizations i am a member. the image is on hub now
* in docker/config.json i see the auth key `sudo cat ~/.docker/config.json` that allows docker cli to login to dockerhub as me
* if i tag the image i pushed with a different tag eg `sudo docker image tag <username>/nginx <username>/nginx:testing` and push it to hubit does not push as image is the same. but in the image(repo) page on hub i see the new tag in the tag tab 
* if i want my repo to be private on dockerhub i create the repo/image first on dockerhub as private and then push to it

### Lecture 36 - Building Images: The Dockerfile Basics

* [dockerfile ref](https://docs.docker.com/engine/reference/builder/)
* we open the dockerfile-sample-1/Dockerfile
* by default dockerfile is named *Dockerfile* we can use anohter name 
`sudo docker build -f some-dockerfile`
* FROM command is required to be present and defines the minimum linux distribution to be used. usually debian:jessie or alpine in recent images. what we specify are actually docker images following the docker naming pattern
* the main benefit from them is to use their package managemnt system to install software (apt or yam) 
* ENV is for environment variables (keyvalue pairs)
* RUN specifies shell commands to be used when running the image (install sw run shell scripts)
* here run sets the package repository to get the nginx for installation
* every RUN is a layer. so we use && to chain commands to avoid many layers 
* the next RUN command points tlogfiles to stdout and stderr. this is the proper way for loging in docker containers. as docker captures them
* EXPOSE exposes ports to the outside world. so no tcp/ip prots are open unless we specify them. these ports live in the virtual networks
CMD is a required parameter as it specs the command we will use to launch a new container

### Lecture 37 Building Images: Running Docker Builds

* the dockerfile is used to build an image. 
* what it says is get the debian:jessie image from dockerhub execute the steps and cache each layer one by one
* the command i use is `sudo docker image build -t customnginx .`
* this says to build an image tagged as customnginx in the current dir using the dockerfile with default name *Dockerfile* in the dir.
* the image is added on the image list but i dont see any build artifacts in the folder
* tagname is custom as it wont be ppushed to the hub, -t sets the tag
* if we change the dcockerfile the unchanged layers are not rebuilt . they are cached. only the changed ones. so we get lighting fast built times. to see it i add one more port in EXPOSE and rebuild. it builds FAST. in image list i see an new image id was created
* at the top of the dockerfile we put things that change the least and in the end things that change more

### Lecture 38 - Building Images: Extending Official Images

* we use dockerfile-sample-2/Dockerfile. here we see only 3 stances (steps or layers). we use the official nginx image to start from. this is ok. if dont need some tweaks. it saves time and effort. 
* we use WORKDIR step to change dir (cd command) or set the wrokdir
* we use COPY to copy our localfile to the server (workdir)
* we dont use CMD. we inherit it from the upstram image. WE DONT INHERIT the ENV though
* we build the image `sudo docker image build -t nginx-with-html .`
* we run a container from it `sudo docker container run --rm -p 80:80 nginx-with-html`  and visit localhost to see the webpage

### Lecture 39 - Assignemt:Build our own Dockerfile and run containers from it

* dockerfiles are part process workflow and part art.
* we take an existing node.js app and dockerize it
* make a Dockerfile. build it, test it, push it, rm it, run it
* use the alpine version of the official node 6.x image
* at it and push it to dockerhub, remove it from local store and run again from hub
* working docker file

```
FROM node:6-alpine
EXPOSE 3000
RUN apk add --update tini \
	&& mkdir -p /usr/src/app
WORKDIR /usr/src/app
COPY package.json ./
RUN npm install \
	&& npm cache clean --force
COPY app.js ./
COPY ./public ./public
COPY ./routes ./routes
COPY ./views ./views
COPY ./bin ./bin
CMD /sbin/tini -- node ./bin/www
```

* teacher uses 

```
COPY . . (all to all)
CMD ["/sbin/tini","--","node","./bin/www"] (no spaces when using string array)
```

* i run it with `sudo docker container run --rm -p 80:3000 --name test achliopa/node-app`

## Section 4 - Container Lifetime & Persistent Data: Volumes,Volumes,Volumes

### Lecture 41 - Container Lifetime & Persistent data

* [immutable infra](https://www.oreilly.com/ideas/an-introduction-to-immutable-infrastructure)
* [12 factor app](https://12factor.net/)
* [12 fractured apps](https://medium.com/@kelseyhightower/12-fractured-apps-1080c73d481c)
* define the problem of persistent data, key concepts with containers: immutable, ephemeral. learning and using data volumes, learn and use build mounts, assignemeents
* containers are *usually* immutable(unchangable) and ephemeral
* *immutable infrastructure*: only re-deploy containers, never change
* what about databases or unique data generated?
* docker gives feats to ensure *separation of concerns* aka storer data in different loacation than binaries
* when we stop a container data are not lost. when we remove it data it contains get lost.
* two ways to solve persistent data problem: Volumes and Build Mounts
* Volumes: make special location outside the container UFS(union filesystem)
* Build Mount: link container path to host path

### Lecture 42 - Peristent Data: Data Volumes

* we use them adding the VOLUME command in Dockerfile e.g `VOLUME /usr/lib/mysql`. it tells docker upon starting a container to create volume and attach it to the container at the specified path. whatever we put there will outlive the container untill we manually delete the volume
* `sudo docker volume prune` cleansup unused volumes
* we pull mysql to cache
* we inspect the image to see its config file, we see a spec for volume data similar to Dockerfile commnd

```
 "Volumes": {
                "/var/lib/mysql": {}
            },
```

* we start a container from the image `sudo docker run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWOR=True mysql`

* we inspect the container to see that a mount entry is added to the metadata

```
"Mounts": [
            {
                "Type": "volume",
                "Name": "ae73a6ede4bb9138d2cae53fd8a4f12c6156e43348ed189f7cdeda7310683f5a",
                "Source": "/var/lib/docker/volumes/ae73a6ede4bb9138d2cae53fd8a4f12c6156e43348ed189f7cdeda7310683f5a/_data",
                "Destination": "/var/lib/mysql",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
        ],

```

* what mounts shows is the container getting its own location on the host to store volume data and in the background it is mounted at the location in the container specified by the VOLUME commnd in dockerfile

* we can list the volumes in the host with `sudo docker volume ls`
* we can inspect a volume with `sudo docker volume inspect <volumename>`
* in linux host we can go to mountpoint and see the volume data
* we see it is difficult from volume to find the container it belongs to. only from container metadata we can find the volume
* volumes outlive executables
* named volumes are a way to connect semanticaly containers and volumes so we know what to delete. this is done with the -v <volumename>:<VOLUME path> e.g `sudo docker run -d --name mysql2 -e MYSQL_ALLOW_EMPTY_PASSWORD=True -v mysql-db:/var/lib/mysql mysql` this assigns a name to the volume. this friendly name is easier to maintain and appears in container metadita with inspoect in mount section
* with `sudo docker volume create` we can create a volume ahead of time

### Lecture 43 Persistent Data:Bind Mounting

* bind mounting: maps a host file or directory to a container file or directory
* we have two locations pointing to the same file
* skips UFS, host files overwrite ones in container
* it is not included in the  Dockerfile, it is added in container run `... run -v /host/path:/path/container`
* it is useful in development environments when our container interacts with frequently changed files on the host
* to show it in action we emulatea copy and a working deirectory set in Dockerfile (sample-2) witha dynamica ssociation to the current dir with bind mounting `sudo docker container run -d --name nginx -p 80:80 -v $(pwd):/usr/share/nginx/html nginx` 
* in the current dir we have and index,html file so we expect to see it rendered in browser. sucess. we change the file on host refresh and see the change
* if we enter the container and run terminal and go to the mapped dir we see the files . if we add on host files we see them added in container

### Lecture 44 - Assignment: Database Upgrades with Named Volumes

* database updates with containers
* best practice in docker is replace container to a lter version
* we create apostgres container with named volume psql-data using version 9.6.1. consult docker hub docs on VOLUME
* check logs to see that db has started volume is there, stop container
* createa new postgres cont with same named volume using 9.6.2
* check logs to validate.
* this works only with patch versions. SQL DBs need manual commands to update to major.minor versions. db limitation

```
 sudo docker container run -d --name postgres -v psql-data:/var/lib/postgresql/data postgres:9.6.1
 sudo docker container logs -f postgres
 sudo docker container stop postgres
 sudo docker container run -d --name postgres2 -v psql-data:/var/lib/postgresql/data postgres:9.6.2
 sudo docker container logs -f postgres2
  sudo docker container stop postgres2
sudo docker container rm -f postgres postgres2
```

### Lecture 46 - Assignemnt: Edit Code Running in Container with Bind Mounts

* [jekyll](https://jekyllrb.com/)
* use jekyll static site generator to start a local web server
* this bridges the gap  of local file acces and running in containers
* tutor has a ready image for us, we run it with `docker contianer run -p 80:4000 -v $(pwd):/site bretfisher/jekyll-serve`

## Section 5 - Making it Easier with Docker Compose: The MultiContainer Tool

### Lecture 48 - DOcker Compose and the docker-compose.yml file

* [yaml format](http://www.yaml.org/start.html)
* [yaml quickref](http://www.yaml.org/refcard.html)
* [compose spec](https://docs.docker.com/compose/compose-file/compose-versioning/)
* we use compose to configure relationships between containers
* we use it to save our docker run settingsin easy to read files
* we use it to create one-liner developer environment startup commands
* compose is made of 2 parts
* a YAML file that describes our solution options for: containers, networks, volumes
* a CLI tool *docker-compose* used for local dev/test automation using the YAML file
* docker-compose.yml has its own versions 1,2,2.1,3,3.1'[
* yaml file can be used with docker-compose command for local docker automation
* with docker command directly in production with Swarm 
* `docker-compose --help` for help, 
* docker-compose.yml is the default filename. a custom yaml file can be used with `docker-compose -f`
* a template yaml compose file is in compose-dample-1 folder. with the term services we mean containers. in yaml list elements are writen with dashes
* if we want to tun the last assignemnt with jekyll with compose the yaml file would be:

```
version: '2'

# same as 
# docker run -p 80:4000 -v $(pwd):/site bretfisher/jekyll-serve

services:
  jekyll:
    image: bretfisher/jekyll-serve
    volumes:
      - .:/site
    ports:
      - '80:4000'
```

* compose-2 and compose-3.yml files are more complex

### Lecture 49 - Tring out Basic Compose Commands

* [compose github](https://github.com/docker/compose/releases)
* cli comes bundled with docker engine in mac/windows but is separate in linux
* its not production grade but great for local development and test.
* common commands are `docker-compose up` setup volume networks and start all containers, `docker-compose down` stop all containers and remove cont/vol/net 
* if our project has a Dockerfile and docker-compose.yml then startind a project in dev environment would be

```
git clone github.com/some/sw
docker-compose up
```

* in compose-sample-2 i check the docker-compose.yml file. it creates 2 containers seting a volume to nginx for nginx.conf file

```
version: '3'

services:
  proxy:
    image: nginx:1.13 # this will use the latest version of 1.13.x
    ports:
      - '80:80' # expose 80 on host and sent to 80 in container
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf:ro
  web:
    image: httpd  # this will use httpd:latest
```

* we docker-compose it up, -d runs it in background, nginx is acting as proxy to the apache server
* `docker-compose logs` shows logs
* `docker-compose ps` shwows running processes `docker compose top` a formatted list. `docker-compose down` to stop all

### Lecture 50 - Assignement: Build a Compose File For a Multi-Container Service

* [compose file ref](https://docs.docker.com/compose/compose-file/)
* build a basic compose file for a drupal cms website. 
* use drupal with postgres. expose drupal with ports to 8080
* set POSTGRES_PASSWORD for postgres. walk through drupal setup in browser
* drupal assumes DB is localhost, but it is the service name as they dont run in same container
* use volumes to store drupal unique data
* we check drupal in dockerhub for volumes and exposed ports and postgres for password setup
* my yaml is 

```
version: '2'

services:
  drupal:
    image: drupal
    ports:
      - 8080:80
    volumes:
      - drupal-modules:/var/www/html/modules
      - drupal-profiles:/var/www/html/profiles
      - drupal-sites:/var/www/html/sites
      - drupal-themes:/var/www/html/themes
  postgres:
    image: postgres
    environment:
      - POSTGRES_PASSWORD=rootroot
volumes:
  drupal-modules:
  drupal-profiles:
  drupal-sites:
  drupal-themes:
```

* i docker-cmompose up 
* enter localhost:8080 and set options in drupal db name and user: postgres
* drupal cannot connect to postgres because it searches it in localhost . in advanced setting we set host to postgres (container name) and success! we have a working drupal server
* to cleanup we run `docker-compose down -v` top remove also the volumes

### Lecture 52 - Adding Image BVuilding to Compose Files

* [compose build options](https://docs.docker.com/compose/compose-file/#build)
* docker compose can build custom images at runtime
* will build them with docker-compose up if it cannot find them in cache.
* we can rebuild the images when we change them with `docker-compose build` or `docker-compose up --build`
* this is good for complex builds that have lots of vars or build arguments
* in compose-sample-3 we have a ready docke-compose.yml file. there we add the build: param to the proxy service specifying the dockerfile(custom name) amd the target image to be build. so we can use the compose file to build our custom image before starting the container
* in the same file we bind mount an html folter from the host containing the artifacts to the apache server
* the actual copy of nginx.conf to the container dir is happening in the dockerfile
* compose.yml will look for  nginx-custom in cache. if its not there it will build it from compose.yml orders
* i docker-compose up and launch the servers. It works
* combining nginx with httpd is done to emulate a production environment
* docker-compose down does not by default remove image we need the --rmi flag with the local name. to use local i need to not give the image i build a name so that ituses the local naming convention. i remove it from the compose file and rebuild. i docker-compose --rmi local and image is gone. using all will delete all images used in the build from cache even httpd that we might use in other projects

### Lecture 53 - Assignment:Compose For Run-Time Image Building and Multi-COntainer Development

* we build a custom drupal image for local testing
* compose is not only for devs but for testers as well
compose is great for testing frameworks and apps
* we use compose file from previous assignemtn . we add a Dockerfile
* see the Readme.md

* docker-compose.yml

```
# create your drupal and postgres config here, based off the last assignment
version: '2'

services:
  drupal:
    image: custom-drupal
    build: .
    ports:
      - 8080:80
    volumes:
      - drupal-modules:/var/www/html/modules
      - drupal-profiles:/var/www/html/profiles
      - drupal-sites:/var/www/html/sites
      - drupal-themes:/var/www/html/themes
  postgres:
    image: postgres:9.6
    environment:
      - POSTGRES_PASSWORD=rootroot
    volumes:
      - drupal-data:/var/lib/postgresql/data
volumes:
  drupal-modules:
  drupal-profiles:
  drupal-sites:
  drupal-themes:
  drupal-data:
```

* dockerfile

```
# create your custom drupal image here, based of official drupal
FROM drupal:8.5
RUN apt-get update \
	&& apt-get install -y git \
	&& rm -rf /var/lib/apt/lists/*
WORKDIR /var/www/html/themes
RUN git clone --branch 8.x-3.x --single-branch --depth 1 https://git.drupal.org/project/bootstrap.git \
	&& chown -R www-data:www-data bootstrap
WORKDIR /var/www/html
```

* command: `docker-compose up` install drupal and connection to postgres like 1st assignment, `docker-compose -v`
* -y flag in apt-get install says yes to the propmpts during installation
* --single-branch --depth 1 in git clone, gets only last commit of the specified branch
* docker works as root in its containers so if we want a special user for an app we need to chown -R (for subfolders)

## Section 6 - Swarm Intro and Creating a 3-Node Swarm Cluster

### Lecture 55 - Swarm Mode: Built-In Orchestration

* [swarm topology video](https://www.youtube.com/watch?v=dooPhkXT9yI)
* [swarm orchestration video](https://www.youtube.com/watch?v=_F6PSP-qhdA)
* [topology management](https://speakerdeck.com/aluzzardi/heart-of-the-swarmkit-topology-management)
* the promise of docker is to deploy microservices in a uniform way everywhere (cloud, local machines)
* small organizations or solo developers have problem managing multiple nodes. Common Problems/Quastions:
	* automate container lifecycle
	* easily scale out/in/up/down
	* ensure containers are recreated if they fail
	* replace containers without downtime (blue/green deploy)
	* track/control containers startup
	* create cross-node virtual networks
	* ensure trusted servers to run containers
	* store secrets, keys, passwords. get them to right container
* SWARM MODE
	* a clustering solution built inside Docker
	* Added in v1.12 (summer 2016) via SwarmKit toolkit
	* Enhanced in v1.13 (Jan 17) via Stacks and Secrets
	* Not enabled by default. Once enabled new commands are available `docker swarm` `docker node` `docker service` `docker stack` `docker secret`
* There are Manager Nodes and Worker Nodes. Managers have Certificate Authority. Communication is Encrypted (TLS). Nodes are Cloud VMs or Physical Machines
* Managers can be also  workers. nodes can be demoted or promoted to each role
* there is an internal distributed state store for swarm
* a manager is a worker with permission to control the swarm
* with `docker container run` we can run only one container. it does not have provisions how to scale up or out
* a service can have multiple tasks. each task will launch a container. managers decide where on the swarm to place them
* a whole new api implements the service in swarm mode. `docker service create` starts the manager node and RAFT DB:
	* API: Accepts commands from client and creates service object
	* Orchestrator: Reconcilliation loop for service objects and creates tasks
	* Allocator: Allocates IP addresses to tasks
	* Scheduler: Assigns Nodes to tasks
	* Dispatcher: Checks in on Workers
* on worker node :
	* Worker: connects to dispatcher to check on assigned tasks
	* Executor: Executes the tasks assigned to worker node

### Lecture 56 - Create your first Service and Scale it locally

* [deploy to swarm](https://docs.docker.com/engine/swarm/services/)

* on our local machine we can start a 1 node docker swarm for testing
* we test our docker install for swarm existence with `docker info`. swarm is inactive
* we enable it with `sudo docker swarm init` this adds our machine as a manger node in a swarm (joins the swarm). DONE
* what this command does: lots of Publik Key Infra and Security Automation
	* Root Signing Certificate created for the swarm
	* certificate is issued for first Manager node
	* join tokens for nodes are created
* also it creates a RAFT database to store root CA, configs and secrets
	* db is encrypted by default on disk (v1.13+)
	* no need for another key/value system to hold orchstration/secrets
	* replicates logs amng managers via mutual TLS in control plane
* Raft is a protocol that ensures consistensy among multiple nodes, perfect for cloud [raft](https://raft.github.io/)
* No need for separate key infrastucture
* we can get a list of our swarm nodes with `docker node ls`
* docker node group of commands gives the ability to manage nodes (promote,demote, inspect, list, rm, update)
* docker swarm group of commands maanges our node (running the cli) relation to the swarm he is participating to (join, leave, init, keys)
* docker service group of commands manages the service/s running on the swarm, it is more or less a docker run similar but for a swarm istead of a one localmachine.
* we create a service with `sudo docker service create alpine ping 8.8.8.8` this service gets an id 
* we can see in `docker service ls`. it gets a random name and it spawned 1 replica out of 1 (default). the first number is how many replicas are actually running  and the nuber on the roght is how many we specified that can be created (max)
* with `docker service ps <service name>` we can get more info on each replica. the task it is rnning, the node name, its name
* if we now run `docker container ls` we see the task as a local service with a name <service>.<replica_num>.<replica id>
* we can scale up the service to 3 replicas with `sudo docker service update <Service ID> --replicas 3`
* docker service ls and ps confirm that service now runs 3 replicas/tasks on host
* docker update group of commands manages resources of container. very useful on swarms
* docker service update command gives much more options, as its goal is to allocate resources and manage containers on the fly woithout stoping them
* if i remove one task with  docker container rm -f, swarm wll see it and launch a new one. service ps shows the history
* to remove all containers we have to remove the service completely, service is gone but contianers still run for a while till theya re stoped

### Lecture 58 - Creating a 3-Node Swarm Cluster

* [digital ocean coupon](https://www.digitalocean.com/?refcode=b813dfcad8d4&utm_campaign=Referral_Invite&utm_medium=Referral_Program&utm_source=CopyPaste)
* [ssh in do](https://www.digitalocean.com/community/tutorials/how-to-use-ssh-keys-with-digitalocean-droplets)
* [docker swarm firewall](https://www.bretfisher.com/docker-swarm-firewall-ports/)

* Available option to deploy multiple nodes
	* play-on-docker.com is good for testing nodes in swarm but runs for 4 hours
	* docker-machine + Virtual boxes (needs >=8gb ram)
	* digital ocean + docker install (mimics production setup)
	* our own build: docker-machine has machines for AWS, Azure, DO, GoogleCloud (use get.docker.com)
* we dont use docker-machine in production
* if we hav edocker-machine on our system we install virtual box. then with `docker-machine create nodeX` we create our nodes on our system
* once we have these nodes we can access them with `docker-machine ssh nodeX` or we can set docker CLI on host to see the node with `docker-machine env nodeX` and `eval $(docker-machine env nodeX)`
* we go to DigitalOCean, we go for 10$ option with ubuntu 16. we add our sssh key and create 3 machines called node1 2 3.
*  i `ssh root@<ip>` to the nodes
* to install docker on the noes we use the script from get.docker.com
and run the followinf commands in the 3 nodes

```
curl -fsSL get.docker.com -o get-docker.sh
sh get-docker.sh
```

* we have docker on our nodes so now we want to start the swarm. swarm in cloud servers asks for an ip so that it is visible from other nodes. we use the public ip of the host. `docker swarm init --advertise-addr <IP addr>`
* we copy the swarm join command from node 1 (manager) console and copy it to node 2

```
docker swarm join --token SWMTKN-1-61sezz5a5u896kpvt5x8ejuonfeki1qtjvr3ji3k7ju7suiqni-bu7prqpxpn9vfqybxjh3zqc7l 46.101.193.122:2377
```

* node 2 is now a worker to the swarm
* `docker node ls` is available only to the manager node1 and lists the nodes. workers have no swarm commands available. 
* if i want to promote a node from worker to manager i have to do it from the manager node `docker node update --role manager node2`
* node1 is still the Leader and node2 reacable. swarm commands a re avaialble to nod2
* we will add node3 as swarm manager by default from node1 using `docker swarm join-token manager` and get the manager join command and token in console

```
docker swarm join --token SWMTKN-1-61sezz5a5u896kpvt5x8ejuonfeki1qtjvr3ji3k7ju7suiqni-f0b60d1zyr7077whwsxlbu2l7 46.101.193.122:2377
```

* we cp it in node3 console. node1 is still the leader (original node)

* token and join commands are invoked with the previous command for manager and worker. no need to store the keys somewhere. also keys can be rotated for security reasons
* we create a service like before specifying size 3 replicas. `docker service create --replicas 3 alpine ping 8.8.8.8`
* if we ps the service we see that each repolica is running on a node
* with `docker node ps` we see the task running on the specific node if i add the node name i see the task running on that node

## Section 7 - Swarm Basic Features ansd How to use them in your Workflow

### Lecture 59 - Scaling Out with Overlay Networking

* swarm introduces overlay multi-host networking. we invoke it with `--driver overlay` when creating a virtual network. it is a swarm wide bridge network
* it handles intra swarm container-to-container communication inside a single swarm
* we can enable IPSec (AES) on network creation (off by default for performance)
* each service can connect to multiple networks (e.g frontend / backend)
* we create an overlay network for a drupal multihost service. `docker network create --driver overlay mydrupal`
* we create a postgres service to run in the mydrupal network `docker service create --name psql --network mydrupal -e POSTGRES_PASSWORD=rootroot postgres`, we check it with docker service ls and ps. it runs on node2
* i create a drupal service in the same network `docker service create --name drupal --network mydrupal -p 80:80 drupal`. it exports port 80 and runs on node1.
* drupal and postgres talk to each other using their service names as hostnames as they are in the same virtual network.
* i can access drupal webserver in my browser with any of the node public ips. 
* i install drupal and connect it to postgres like we did before. i can access it from any ip. site externaly looks like it is runnign on all nodes but i know it runs only in node1. if we inspect the service we see it has only 1 ip on the virtual netwrk so why we can access it from all public ips?

### Lecture 60 - Scalling Out with Routing Mesh

* [routing mesh](https://docs.docker.com/engine/swarm/ingress/)
*  the prvious question is answered by the Routing Mesh which routes ingree (incoming) packets for a Service to the proper task
* this is the ingress overlay network appearing in the network list with swarm scope (spans all nodes)
* uses IPVS from linux kernel
* load balances swarm services accross all node tasks and listens all nodes for traffic
* 2 examples of its functionality:
	* container-to-container comm in an overlay nw (using VIP) e.g server cluster
	* external in/out traffic to published ports (ALL NODES LISTEN). all nodes listen but route the traffic to the node with least load/traffic using load balancing
* if i have a 3 replica swarm service running as 3 tasks/containers on 3 nodes with different ips. it created a new virtual network IP for the service (using its name as host name for DNS). Everybody that wants to talk to this service internallyuses this VIP and load balancer does the rest, this DNS routing is like RoundRobin (we can use it instead) but its superior as it mimics a HW load balancer
* anyone using a nodes public ip and the published port goes to the swarm load balancer attached to the external ip address. this load balancer decides where to assing the request using the virtual network ip addresses
* to see it in action we create a 3 replica elasticsearch service. `docker service create --name search --replicas 3 -p 9200:9200 elasticsearch:2`. i check it with `docker service ps search`
* as the port 9200 is published in node1 i can see it talking with `curl localhost:9200`. i hit it multiple times and get a different name each time. these are the 2 node tasks talking with loadbalancer sending my request to a node.
* at Docker v1.13 Routing Mesh Cont. is STATELESS Load balancing. so if we consistently want to talk to a SPECIFIC task then we need tweaking
* this LoadBalancer is OSI Layer 3 (TCP) not layer 4 (DNS)
* To overcome this limitation we can use Nginx or HAProxy LB proxy
* Docker EE comes with built-in L4 web proxy

### Lecture 61 - Assignemtn: Create a Multiserrvice Multinode Web App

* we will use a multilayer app for this assignelment. we will use the Docker's [Distributed Voting App](https://github.com/dockersamples/example-voting-app)
* we will use the swarm-app-1 dir 
* we need 1 volume, 2 networks 5 services
* a good approach is to test scripts locally before running them in production
* we never build images in production. we build them using CI and push them to dockerhub
* see the readme.md for steps and instructions (relalife case of sw architect that is docker agnostic)

* step 1: create and list overlay networks

```
docker network create --driver overlay backend
docker network create --driver overlay frontend
docker network ls
```

* step 2: create the vote service

```
docker service create --name vote --replicas 2 -p 80:80 --network frontend dockersamples/examplevotingapp_vote:before
```

* step 3: create redis keystore (1 replica)

```
sudo docker service create --name redis --network frontend redis:3.2
```

* step 4:create db, our source is a named volume

```
docker service create --name db --network backend --mount type=volume,source=db-data,target=/var/lib/postgresql/data postgres:9.4
```

* setp 5: create worker and attach to 2 networks

```
docker service create --name worker --network backend --network frontend dockersamples/examplevotingapp_worker
```

* step 6: add resiutls

```
docker service create --name result -p 5001:80 --network backend dockersamples/examplevotingapp_result:before
```

* option -v is not supported in service commands
* we use ps to see the services
* we use docker servicce logs <servicename> to check tyhe logs

### Lecture 63 - Swarm Stacks and Production Grade Compose

* [feats not supported in stack deploy](https://docs.docker.com/compose/compose-file/#not-supported-for-docker-stack-deploy)
* at v1.13 docker added a layer of abstration on swarms called stacks
* stacks are compose files for production swarms
* stacks accept compose files as their declartative definition for services,networks and volumes
* we use `docker stack deploy` much like `docker-compose up` to impor the stack file and run the commands in it
* stack manages  all objects for us. like overlay network per stack adds stack name to start of their name
* we can use existing networks etx using *external* in the file
* in our stack compose file we use *deploy:* and not *build:*
* building should not happen in aswarm. build must happen locally and pushed to dockerhub
* compose now ignores deploy: swarm igoners build: 9so we can use same file for development and production
* docker-compose cli not needer on a swarm node
* stack yaml compose file can have multiple services and volumes and overlay networks
* stack is for 1 swarm
* our whole previous assignemtn app creation on swarm using commands can be replaces by a single stack file

```
version: "3"
services:

  redis:
    image: redis:alpine
    ports:
      - "6379"
    networks:
      - frontend
    deploy:
      replicas: 1
      update_config:
        parallelism: 2
        delay: 10s
      restart_policy:
        condition: on-failure
  db:
    image: postgres:9.4
    volumes:
      - db-data:/var/lib/postgresql/data
    networks:
      - backend
    deploy:
      placement:
        constraints: [node.role == manager]
  vote:
    image: dockersamples/examplevotingapp_vote:before
    ports:
      - 5000:80
    networks:
      - frontend
    depends_on:
      - redis
    deploy:
      replicas: 2
      update_config:
        parallelism: 2
      restart_policy:
        condition: on-failure
  result:
    image: dockersamples/examplevotingapp_result:before
    ports:
      - 5001:80
    networks:
      - backend
    depends_on:
      - db
    deploy:
      replicas: 1
      update_config:
        parallelism: 2
        delay: 10s
      restart_policy:
        condition: on-failure

  worker:
    image: dockersamples/examplevotingapp_worker
    networks:
      - frontend
      - backend
    deploy:
      mode: replicated
      replicas: 1
      labels: [APP=VOTING]
      restart_policy:
        condition: on-failure
        delay: 10s
        max_attempts: 3
        window: 120s
      placement:
        constraints: [node.role == manager]

  visualizer:
    image: dockersamples/visualizer
    ports:
      - "8080:8080"
    stop_grace_period: 1m30s
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
    deploy:
      placement:
        constraints: [node.role == manager]

networks:
  frontend:
  backend:

volumes:
  db-data:
```

* stack files must be v3 or higher
* deploy metadata gives more fine grain control on deploys, how to treat updates, restarts etc, or add contstrains to run on a specific node

```
    deploy:
      replicas: 1
      update_config:
        parallelism: 2
        delay: 10s
      restart_policy:
        condition: on-failure
```

* we insert the stack file with vim at node1 and run it `docker stack deploy -c example-voting-app-stack.yml voteapp`
* docker stack command set is limited
* docker stack is seen as an entity in docker i can see its tasks with `docker stack ps <stackname>` and where they are runnin on
* i can see the stack services with `docker stack services <stackname>` like docker service ls
* we see that appname precedes all names
* manually typing commands to change our stack is a nono. we should change the file instead
* to redeploy stack after cahnging the yaml file i repeat the deploy command

### Lecture 64 - Secrets Storage for Swarms: Protecting the env variables

* a new feat at v1.13.1 is secrets
* its the easiest secure solution for storing secrets in a swarm
* it is encrypted on disk , in transit and is used where it needs to be.
* a secret might be: usernames and passwords, TLS cerificates and keys SSH keys, api keys. any data we dont want unwanted eye to see.
* VAULT is good for storing secrets but is a different installation and needs integration . is not built in
* Secrets storage supports genric strings or binaries up to 500Kb in size, 
* dos not require apps to be rewritten
* as of 1,13,0 Swarm Raft Db is encrypted on disk.
* only stored on dosk on manager nodes
* the way keys get to containers is through the control plane or encrypted communication (TLS + Mutual Auth) between managers and workers
* secrets a re first sttored in Swarm raft DB the assigned to services
* they look like files in a container but are actual in-memory fs
in `/run/secrets/<secret_name>` or `/run/secrets/<secret_alias>` as kkey value pairs where key is the name and value the content
* if we use secrtets localy we can add them to the compose file. it can use file based secrets but not secure
* if you dont have swarms you cant use secrets but docker-compose fakes it in sake of having uniform yaml files for dev and prod. it copies the secrets to normal text files


### Lecture 65 - Using Secrets in Swarm Services

* [docker secrets](https://docs.docker.com/engine/swarm/secrets/)
* there are 2 ways we can createa secret in a swarm. using a tfile or passing it in commandline
* we go for 1st option. in node1 in the *secrets-sample-1* folder where the file to be added resides we run `docker secret create psql_user psql_user.txt`
* we add the password as a command to the secrets `echo "myDBpassWORD" | docker secret create psql_pass -` dash says the command to read the secret from stdin
* the most secure way is to add the secrets through files and then remove the files
* i can see the secrets with `docker secret ls` and inspect them. i dont see the original value though
* we use the secrets by creating a service and passing them as env parameters `docker service create --name psql --secret psql_user --secret psql_pass -e POSTGRES_PASSWORD_FILE=/run/secrets/psql_pass -e POSTGRES_USER_FILE=/run/secrets/psql_user postgres` the command says take the secret and assign it to the service so that its containers have access to it, this maps secrets to service but it doesnt tell postgress service how to use them. the -e flag does that passing the secrets as env params as files
* psql runs on node2. so we will test there through bash `docker exec -it psql.1.tkzjulxny00edvgh9lr27ufw5 bash` through bash i run `ls /run/secrets` and see the 2 files if i more them i see the vlas unencrypted
* if i remove the secret from the service `docker service update --secret-rm`, the service will redeploy the containers (immutable design) will not update them it will create new ones. not good for DBs. need to come up with a plan how to update passwords in databases

### Lecture 66 - Using Secrets with Swarm Stacks

* [compose file secrets](https://docs.docker.com/compose/compose-file/#secrets-configuration-reference)
* now that we know how to add secrets to services we will see how to add them in stacks.fa-vinewe work on *secrets-sample-2* folder in node1. we have a yaml compose file for the stack and 2 txt files witht he credentials to add as secrets
* to use secrets in stacks the version has to be 3.1
* in the compose file we have the root secrets key whre the files for the secrets are added  (./ for working dir). to use them in stack yaml file we need to have them in a file or precreated in command line (if we have them precreated instead of file calling we do external calling in secrets key)
* we first set the keys and then add them to the service that needs them
* long form o fsecrets in a compose yaml file adds users, roles and permissions
* we use the file (yaml with secrets added) in a standard deploy command. `docker stack deploy -c docker-compose.yml mydb`
* when i add secrets with stack when i remove the stack `docker stack rm mydb` secrets are removed as well. when ai manually add secrets ato a service i have to delete them manually
* in prod environments we must clean files and bash logs with secrets

### Lecture 67 - Assignemnt: Create a Stack w/ Secrets and Deploy

* we will use the drupal compose file from *compose-assignment-2* and extend it with secrets
* rename image back to official drupal:8.2
* remnove build:
* add secret via external: (command line)
* use environment var POSTGRES_PASSWORD_FILE
* add secret via cli `echo "<pw>" | docker secret create psql-pw -`
* copy compose file into new yaml file on our swarm node1
* solution - yaml file

```
# create your drupal and postgres config here, based off the last assignment
version: '3.1'

services:
  drupal:
    image: drupal:8.2
    ports:
      - 8080:80
    volumes:
      - drupal-modules:/var/www/html/modules
      - drupal-profiles:/var/www/html/profiles
      - drupal-sites:/var/www/html/sites
      - drupal-themes:/var/www/html/themes
  postgres:
    image: postgres:9.6
    secrets:
      - psql-pw
    environment:
      POSTGRES_PASSWORD_FILE: /run/secrets/psql-pw
    volumes:
      - drupal-data:/var/lib/postgresql/data
volumes:
  drupal-modules:
  drupal-profiles:
  drupal-sites:
  drupal-themes:
  drupal-data:
secrets:
  psql-pw :
    external: true
```

* commands 

```
echo "rootroot" | docker secret create psql-pw -
docker stack deploy -c docker-compose.yml myapp
```

* test in browser enter drupal set posgres credential (dont forget the hostname myapp_postgres) and it works!!!!!!

## Section 8 - Swarm App Lifecycle

### Lecture 69 - Using Secrets with Local Docker Compose

* back in the localmachine we check *secrets-sample-2* folder
* localhost is not a swarm node. we check that with `sudo docker node ls`
* we do `docker-compose up -d` to run the containers
* we run `sudo docker-compose exec psql cat /run/secrets/psql_user
` to add the secret in the container
* its not actually a secret but is used as in local dev. what it does is it bindmounts the secret from host to the container fs to be used as env param
* this works in docker-compose v11 and only with file based secrets not external

### Lecture 70 - Full App Lifecycle: Dev, Build and Deploy With a Single Compose Design

* [multiple compose files](https://docs.docker.com/compose/extends/#multiple-compose-files)
* [compose file in prod](https://docs.docker.com/compose/production/)
* as our apps grow we end up with multiple compose files. our goal is to have a single set for dev and prod. and then use them with: 
	* local `docker-compose up` for dev environment
	* remote `docker-compose up` for CI environment
	* remote `docker stack deploy` for prod environment
* we test this flow in *swarm-stack-3* folder where we have 3 compose files for each environment amd a dockerfile for a custom drupal build
* first we have the *docker-compose.yml* with all the presets.this will be used in all the environments
* then we have the *docker-compose.override.yml* that docker will use when i use the command `docker-compose up` the rules are to eaw local development. this will override all settings in barebone docker-compose file. so we use the custom bild from simple file and we add up secrets (file based) port, bind mounts all for local dev
* .por.yml and .test.yml are to be used in production and testing env respectively. override is picked by docker-compose by default. for the .test.yml i have to specify in the docker-compose command with the -f flag manually so that it is used
* for .prod.yml as in prod i dont have docker-compose available i have to previously combine the files into a single one with docker-compose config command
* the .test. is meant to be used in a ci tool like jenkins. iam giving test sttings, ports, secrets but dont care for persisting data as int esting i run from 0 every time for repeatabilit and all gets wiped out after test completes. we see that sample-data are being mount from local fs to container for testing.
* for .prod. things get serioous as we implement our production concerns.
* i run `docker-compose up -d` on my deb machine. it will use the barebone file and then overrlay the override. i inspect the container and i see that it worked as all the mounts set in the override are there. we stop all with `docker-compose down`
* for ci on our test server we have to make sure that docker-compose is available. then in our build steps in the ci tools we must add a shell script `docker-compose -f docker-compose.yml -f docker-compose.test.yml up -d`
* for production i run the `docker-compose -f docker-compose.yml -f docker-compose.prod.yml config > output.yml`. this will bind both files into a single one (output.yml)  to be used in prod stack. this command should run in our ci solution as a build step
* this is very cut-edge stuff so we need to check the outputs for com,peteness. extends: does not work yet on stacks and secrets and not added with config. (it seems to work in 2018)

### Lecture 71 - Service Updates: Changing Things in Flight

* [service update command](https://docs.docker.com/engine/reference/commandline/service_update/)
* service updates provide a rolling replacement pattern of the tasts/containers/replicas in a service
* it limits downtime NOT prevents downtime. 0 downtime only gets verified with testing. simple apps might claim it. but dbs and websockets are tricky stuff. 
* in most cases service updates will wipe out and replace containers if its not for very simple changes (metadata, labels and stuff)
* there are 77 cli options to control the update. many off them have -add and -rm as they are used in multitude and we need to spec which ones we add or remove
* service aupdate includes rollback and healthcheck options
* scale and rollback subcommands are available for fast access `docker service scale <servicename>=4` `docker service rollback <servicename>
* a stack deploy on an existing stack will invoke service updates
* a common pattern is to update the image used to a newer version `docker service update --image <imagename>:<new tag> <servicename>`
* an other example is to add an environment var and remove a port `docker service update --env-add NODE_ENV=production --publish-rm 8080`
* we can scale muliple services at once `docker service scale <service1>=8 <service2>=6`
* we test the concept by creating a service in anode `docker service create -p 8088:80 --name web nginx:1.13.7` we scel it up with `docker service scale web=5`
* we run an update changing the image useed `docker service update --image nginx:1.13.6 web` it updates one b one
* to change ports we cannot just publish on top. we have to  remove the old ones and add the new ones. `docker service update --publish-rm 8080:80 --publish-add 9090:80 web`
* often we have a problem called rebalancing. when tasks are not evened out on nodes. bu doing a service update without specifing any change docker will redistribute the tasks on nodes balancing things. we do this with --force flag. `docker service update --force web`
* we clean up `docker service rm web`

### Lecture 72 - Healthchecks in Dockerfiles

* [laravel defaults](https://github.com/BretFisher/php-docker-good-defaults)
* [HEALTHCHECK Dockerfile](https://docs.docker.com/engine/reference/builder/#healthcheck)
* [healthcheck compose file](https://docs.docker.com/compose/compose-file/#healthcheck)
* HEALTHCHECKS were added in v1.12 as part of swarm toolkit.
is supported in Dockerfile, Compose YAML, docker run, swarm services
* highly recommended in production.
* docker engine will exec the command in the containers (e.g. curl localhost) it works even on the virtual network
* it expects exit 0 (OK) or exit 1 (Error)
* in docker we need exit 1 for error
* default container states are (starting, healthy or unhealthy) if its unhealthy it will retry every 30sec. all this is configurable
* better than just checking that it runs. it can combined with an assertion network in code
* not AN EXTERNAL MONITORING REPLACEMENT
* Healthcheck status shows up in `docker container ls`
