## Docker container with eisfair-ng SDK
 
 This is a docker implementation of eisfair-ng buildnode for Jenkins.

 For more information please refer to 
 [Official website](https://web.nettworks.org/wiki/display/eng/eisfair-ng+Wiki) 
 or [Support forum](https://web.nettworks.org/forum/)

### 1. Install docker

 This instruction works for a <b>Centos7</b> docker host. Other distributions 
 may need some adjustments.

```shell
sudo tee /etc/yum.repos.d/docker.repo <<-'EOF'
[dockerrepo]
name=Docker Repository
baseurl=https://yum.dockerproject.org/repo/main/centos/7/
enabled=1
gpgcheck=1
gpgkey=https://yum.dockerproject.org/gpg
EOF
...
sudo yum install docker-engine -y
...
sudo systemctl enable docker.service
...
sudo systemctl start docker.service
```

### 2. Build/Use the Container

You now have two options: 
- Build from scratch or 
- Pull the ready-made image from DockerHub. 

#### 2a Image from Docker Hub

```shell
sudo docker pull starwarsfan/eisfair-ng-buildnode
```

#### 2b Build from scratch

##### Pull repo from github

```shell
sudo git clone https://github.com/starwarsfan/eisfair-ng-buildnode.git
cd eisfair-ng-buildnode
```

##### Build image

```shell
sudo docker build -t starwarsfan/eisfair-ng-buildnode:latest .
```

### 3. Starting docker container

```shell
sudo docker run \
    --name eisfair-ng-buildnode \
    -d \
    starwarsfan/eisfair-ng-buildnode:latest
```

#### 3.a Mountpoints

There are the following mountpoints available:

 * /data/work/
 * /home/jenkins/.ssh/

These mountpoints can be used to mount folders from the host or other volumes
to store informations which should be persistant or should not everytime be 
recreated.

With the additional run parameter _-v <host-folder>:/data/work/_ you can mount 
a folder on the docker host which contains persistant data. So the run command 
may look like the following example:

```shell
sudo docker run \ 
    --name eisfair-ng-buildnode \
    -v /data/git-clone/:/data/work/ ...
```

#### 3.b Available options

The container could be startet with some of the following options. These list 
contains the default values, which could be overwritten on the docker run
command: 

 * JENKINS_URL=http://localhost
 * JENKINS_TUNNEL=
 * JENKINS_USERNAME=admin
 * JENKINS_PASSWORD=admin
 * EXECUTORS=1
 * DESCRIPTION=Swarm node with fli4l buildroot
 * LABELS=linux swarm fli4l-buildroot
 * NAME=generic-swarm-node

```shell
sudo docker run \
    --name eisfair-ng-buildnode \
    -e "JENKINS_URL=https://jenkins.foobar.org" \
    -e "JENKINS_PASSWORD=123456" ...
```

#### 3.c Jenkins behind reverse proxy

The option JENKINS_TUNNEL might be neccessary if Jenkins is running behind
a reverse proxy as the JNLP connection could not be established in such a 
setup. You need to configure the following on Jenkins:
 
 * Configuration > Global Security > Agents
 * Set _TCP port for JNLP agents_ to _Static_
 * Enter a port number like _32775_
 * Save configuration

Assumed your Jenkins is available on https://jenkins.foobar.org, the host has 
the ip 10.20.30.40 and you configured the JNLP port 32775, you need to start 
the container with at least these options:

```shell
sudo docker run \
    --name eisfair-ng-buildnode \
    -e "JENKINS_URL=https://jenkins.foobar.org" \
    -e "JENKINS_TUNNEL=10.20.30.40:32775" ...
```

### 4. Useful commands

Check running / stopped container:

```shell
sudo docker ps -a
```

Stop the container

```shell
sudo docker stop eisfair-ng-buildnode
```

Start the container

```shell
sudo docker start eisfair-ng-buildnode
```

Get logs from container

```shell
sudo docker logs -f eisfair-ng-buildnode
```

Open cmdline inside of container

```shell
sudo docker exec -i -t eisfair-ng-buildnode /bin/bash
```
