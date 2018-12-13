# Pipeline Sample

## demo 

### Create a new Pipeline project

Prepare an environment for the run

```
DOCKER_UCP_CREDENTIALS_ID=ucp-jenkins
DOCKER_UCP_URI=tcp://172.28.128.3:443
DOCKER_SERVICE_NAME=pipe
```
* Please check DOCKER_UCP_URI in Client Bundle -> env.sh

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


### Run the build

You can watch the output of the Build by clicking on the task number in the Build History and then selecting Build Output.

It will take few seconds to kick off a slave container in worker node...3.2.1

![time](/jenkins/images/time.jpg?raw=true "time")


The console output will show you all the details from the script execution. 

![consoleout](/jenkins/images/consoleout.jpg?raw=true "consoleout")


### Check DTR
Review the repository in DTR. You should now see a new image has been uploaded. 
![dtrresult](/jenkins/images/dtrresult.jpg?raw=true "dtrresult")

