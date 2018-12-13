# Pipeline Sample

## demo 

### Create a new Pipeline project

Prepare an environment for the run

```
DOCKER_UCP_CREDENTIALS_ID=ucp-jenkins
DOCKER_UCP_URI=tcp://172.28.128.3:443
DOCKER_SERVICE_NAME=pipe
```
* you can find DOCKER_UCP_URI in Client Bundle -> env.sh

![pipeconfig](./img/pipeconfig.jpg?raw=true "pipeconfig" )

### Add Pipeline script 

```
node {
    stage('Clone') {
   git 'https://github.com/jzyao/jenkinspipeline'
    }

    stage('Deploy') {
         withDockerServer([credentialsId: env.DOCKER_UCP_CREDENTIALS_ID, uri: env.DOCKER_UCP_URI]) {
            sh "docker stack deploy -c docker-compose-pipeline-jobs.yaml ${env.DOCKER_SERVICE_NAME}"
        }
      }
    }
```

![pipeline](/img/pipeline.jpg?raw=true "pipeline" )

### Run the build

You can watch the output of the Build by clicking on the task number in the Build History and then selecting Build Output.

```
Started by user admin
Running in Durability level: MAX_SURVIVABILITY
[Pipeline] node
Running on Jenkins in /var/jenkins_home/workspace/pipeline-test
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Clone)
[Pipeline] git
 > git rev-parse --is-inside-work-tree # timeout=10
Fetching changes from the remote Git repository
 > git config remote.origin.url https://github.com/jzyao/jenkinspipeline # timeout=10
Fetching upstream changes from https://github.com/jzyao/jenkinspipeline
 > git --version # timeout=10
 > git fetch --tags --progress https://github.com/jzyao/jenkinspipeline +refs/heads/*:refs/remotes/origin/*
 > git rev-parse refs/remotes/origin/master^{commit} # timeout=10
 > git rev-parse refs/remotes/origin/origin/master^{commit} # timeout=10
Checking out Revision c2585aed4dab07fab608158c3e293eaf05c0f84b (refs/remotes/origin/master)
 > git config core.sparsecheckout # timeout=10
 > git checkout -f c2585aed4dab07fab608158c3e293eaf05c0f84b
 > git branch -a -v --no-abbrev # timeout=10
 > git branch -D master # timeout=10
 > git checkout -b master c2585aed4dab07fab608158c3e293eaf05c0f84b
Commit message: "Update docker-compose.yaml"
 > git rev-list --no-walk c2585aed4dab07fab608158c3e293eaf05c0f84b # timeout=10
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Deploy)
[Pipeline] withDockerServer
[Pipeline] {
[Pipeline] sh
+ docker stack deploy -c docker-compose.yaml pipe
Creating network pipe_default
Creating service pipe_build1
Creating service pipe_build2
Creating service pipe_build3
[Pipeline] }
[Pipeline] // withDockerServer
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // node
[Pipeline] End of Pipeline
Finished: SUCCESS
```

### Check Stack from UCP
You should now 3 running services. 
![stack](/img/stack.jpg?raw=true "stack")

For each service, you shold find corresponding volume and loaction
![volume](/img/volume.jpg?raw=true "volume")

