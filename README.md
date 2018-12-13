# Jenkins Guide for Docker EE - Swarm

fork from https://github.com/ollypom/ee-jenkins  
add  Jenkins slave step

## Build your images and push them to DTR (Distributed Trusted Registry)

For this environment we build our own Jenkins Master and Jenkins Slaves Images. The Jenkins Master image is the upstream image + our CA certificates.


```
$ export DTR=https://
$ export VERSION=2.88

$ cd jenkins-master
$ docker build -t ${DTR}/admin/jenkins:${VERSION} .
$ docker push ${DTR}/admin/jenkins:${VERSION} 

$ cd ../jenkins-slave
$ docker build -t ${DTR}/admin/jenkins-slave:${VERSION} .
$ docker push ${DTR}/admin/jenkins-slave:${VERSION}
```

## Start your Jenkins Service

This can step can be done within the UCP UI or by sourcing the Client bundle and using the Docker Client.

```
$ unzip ucp-bundle-admin.zip
$ source env.sh

$ cd jenkins-master
$ docker stack deploy -c docker-compose.yml ci
```

## Configure Jenkins

Browse to the URL of Jenkins http://${UCP}:30000

Take the password from the jenkins container.

```
$ id=$(docker ps -q -f "name=ci_jenkins") 
$ docker exec ${id} cat /var/jenkins_home/secrets/initialAdminPassword
```

Login to the Jenkins appliancing with this password, installing all of the default plugins and confiruing a user.

### Configuring SSH

```
$ docker exec -it ${id} sh
/ $ ssh-keygen
/ $ cat ~/.ssh/id_rsa.pub
```

Copy accross this public key to your Github Account

- Github.com > Settings > SSH Keys


```
/ $ ssh git@github.com
```

### Configuring Github Webhook

Settings > Webooks > Add the payload URL: http://${DTR}:30000/github-webhook/


## Jenkins Plug-Ins Required

- Docker Plugin 
- Git Plugin
- Github API plugin
- GitHub Authentication plugin - Required to use Access Tokens

## Jenkins Slave Setup

In Jenkins go to Manage Jenkins > Configure System 

Create A New Cloud

![cloud](/jenkins/images/5.png?raw=true "Add a new cloud")

Configure docker detail

* choose a dedicated EE worker as Jenkins slave host node. 
* make sure docker host can be connected remotely.

![configure](/jenkins/images/4.png?raw=true "configure cloud detail")

Configure docker template detail

* better to pull slave image in slave node before pipeline.

![configure template](/jenkins/images/2.png?raw=true "configure template detail")
![configure template](/jenkins/images/1.png?raw=true "configure template detail")


Everything is now good to go for your Jenkins Config. 
Your now ready to build your Pipeline Jobs :)

[Pipeline Sample](Pipeline.md)
