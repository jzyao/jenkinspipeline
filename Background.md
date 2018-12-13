# Background

##

Customer will compile same source code with 16 different profiles. The source code is around 200-400Gb, so it will take a very long time to pull code and then compile in the container.

Also, source code can't be shared with different compiling process, since it will generate some tmp file during process.

Customer now has 4 very powerful pthyical machines.

## Design

At begining, make 4 full copy of source code in each pthyical machine and save on different folders, i.e.  `/mydata1 /mydata2 /mydata3 /mydata4`

In each machine, create 4 docker volume and copy data to these volumes, make sure the first part of volume name will be the docker stack name

```
for i in {i..4}
do
  docker volume create pipe_data$i
  docker container create --name dummy -v pipe_mydata$i:/tmp/ hello-world
  docker cp mydata$i/ dummy:/tmp/
  docker rm dummy
done
```

Second, lable each node, in my case, `jenkinsworker:1`, which will be used in docker compose file to pin service to dedicate node.

Third, depoly docker stack to kick off 16 services to run 16 compiling jobs.

Compose file for four containers in two node 

```
version: '3.3'

services:
  build1:
    image: tomcat:7-jre8-alpine
    volumes:
      - type: volume
        source: mydata1
        target: /data
    deploy:
      placement:
        constraints:
          - node.labels.jenkinsworker == 1
  build2:
    image: tomcat:7-jre8-alpine
    volumes:
      - type: volume
        source: mydata2
        target: /data
    deploy:
      placement:
        constraints:
          - node.labels.jenkinsworker == 2
  build3:
    image: tomcat:7-jre8-alpine
    volumes:
      - type: volume
        source: mydata2
        target: /data
    deploy:
      placement:
        constraints:
          - node.labels.jenkinsworker == 1
  build4:
    image: tomcat:7-jre8-alpine
    volumes:
      - type: volume
        source: mydata2
        target: /data
    deploy:
      placement:
        constraints:
          - node.labels.jenkinsworker == 2
volumes:
  mydata1:
  mydata2:
```
