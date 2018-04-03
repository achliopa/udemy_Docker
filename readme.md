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