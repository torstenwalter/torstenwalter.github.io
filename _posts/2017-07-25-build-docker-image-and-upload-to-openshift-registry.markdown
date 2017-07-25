---
layout: post
title:  "How to build a docker image and upload it to Minishift's docker registry?"
date:   2017-07-25 15:30:00 +0200
categories: minishift openshift docker registry
---


- set docker environment variables to connect do Minishift docker daemon
```
eval $(minishift docker-env)
```

- login to openshift
```
oc login -u developer
```

- login to the docker registry using the token from the currently logged-in openshift user
```
docker login -u `oc whoami` -p `oc whoami -t` 172.30.1.1:5000
```

- build the docker image
```
docker-compose build
```
The output looks like:
```
Building jenkins
Step 1 : FROM registry.hub.docker.com/library/centos:centos7
 ---> 36540f359ca3
Step 2 : ...
         ...
 ---> a4628dc93c97
Successfully built a4628dc93c97
```

- tag the newly created docker image
```
docker tag a4628dc93c97 172.30.1.1:5000/myproject/jenkins
```

- push the image to the docker registry
```
docker push 172.30.1.1:5000/myproject/jenkins
```

- verify that the image was uploaded
```
oc get istag
```
output:
```
NAME             DOCKER REF                                                                                                  UPDATED             IMAGENAME
jenkins:latest   172.30.1.1:5000/myproject/jenkins@sha256:6cab7386a27b2a8aa97d181f1d413d54a9fcb6dbfcf9b98d53bcff64ad05726b   About an hour ago   sha256:6cab7386a27b2a8aa97d181f1d413d54a9fcb6dbfcf9b98d53bcff64ad05726b
```


- If you want to give your image a different label then 'latest' you can specify it as well:
```
docker tag a4628dc93c97 172.30.1.1:5000/myproject/jenkins:1.0.0
docker push 172.30.1.1:5000/myproject/jenkins:1.0.0
```
```
oc get istag
```
output:
```
NAME             DOCKER REF                                                                                                  UPDATED             IMAGENAME
jenkins:latest   172.30.1.1:5000/myproject/jenkins@sha256:6cab7386a27b2a8aa97d181f1d413d54a9fcb6dbfcf9b98d53bcff64ad05726b   About an hour ago   sha256:6cab7386a27b2a8aa97d181f1d413d54a9fcb6dbfcf9b98d53bcff64ad05726b
jenkins:1.0.0    172.30.1.1:5000/myproject/jenkins@sha256:0e1b8efd34dc13e721b231cab8c7e07fda5b03a14ef451874580c01fc1c2740d   27 seconds ago      sha256:0e1b8efd34dc13e721b231cab8c7e07fda5b03a14ef451874580c01fc1c2740d
```

- create a new application from the uploaded image
```
oc new-app --image-stream=jenkins --name=jenkins
```

- expose a route for the new application
```
oc expose service jenkins
```
