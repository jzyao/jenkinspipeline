# Jenkins Pipeline Guide for Docker EE 

## Create Jenkins user in UCP and download Client bundle

!user()

## Build your images and push them to DTR (Distributed Trusted Registry)

For this environment we build our own Jenkins Master


## Start your Jenkins Service

This can step can be done within the UCP UI or by sourcing the Client bundle and using the Docker Client.

![jenkinsuser](/img/jenkinsuser.jpg?raw=true "jenkinsuser" =200x)

![clientbundle](/img/clientbundle.jpg?raw=true "clientbundle" =200x)

```
$ unzip ucp-bundle-jenkins.zip
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


## Jenkins Plug-Ins Required

- Docker Plugin 
- Environment Injector Plugin
- Git Plugin
- Github API plugin
- GitHub Authentication plugin - Required to use Access Tokens



[Pipeline Sample](Pipeline.md)
