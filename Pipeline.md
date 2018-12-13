# Pipeline Sample

## demo 

### Create a new Freestyle project

Lock this project to Jenkins slave worker node

![free](/jenkins/images/free.jpg?raw=true "lock")


### Add build step --> Execute shell. 

Please replace the <DTR_URL>, <PASSWORD> with your own


```
DTR_USERNAME=admin
DTR_URL=<DTR_URL>
TAG=1

git clone https://github.com/jzyao/CIrepo.git

cd CIrepo

docker build -t $DTR_URL/admin/citest:$TAG .

docker login -u admin -p <PASSWORD> $DTR_URL

docker push $DTR_URL/admin/citest:$TAG
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

