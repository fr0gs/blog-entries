# Ember & nginx docker deployment with multi-stage builds

## Introduction

At work we use [docker](https://www.docker.com/) as the virtualization technology of choice to perform our project's deployments. We follow a microservices architecture that allows us to do rapid development & testing, quickly trying new ideas and iterating on new functionality using a
modular approach, choosing the best language/framework that best adapts to our needs for each
particular use case.


## The Problem

Our fronted stack consists of **Ember.js** happily running into an **nginx** server inside a docker container. The initial building & deployment process that we had was effective but a little cumbersome. I will use TenForce's [webcat](https://github.com/tenforce/webcat) repository as example.

Initially the Ember application is built via command line (`ember build --prod`), generating a *dist.zip* file. The file is then uploaded to the [repository releases](https://github.com/tenforce/webcat/releases) with a new tag assigned.

Afterwards, when building the **nginx** docker image, from the *Dockerfile* we detect the current version of the frontend reading it from the *package.json* file and fetch it from the github releases, unpacking the zip file contents into the **nginx** serving directory.

The *Dockerfile* is self explanatory:

```
FROM semtech/mu-nginx-spa-proxy

MAINTAINER Aad Versteden <madnificent@gmail.com>

RUN apt-get update; apt-get upgrade -y; apt-get install -y unzip wget;
COPY package.json /package.json
RUN mkdir /app; cd /app; wget https://github.com/tenforce/webcat/releases/download/v$(cat /package.json | grep version | head -n 1 | awk -F: '{ print $2 }' | sed 's/[ ",]//g')/dist.zip
RUN cd /app; unzip dist.zip; mv dist/* .
RUN rm /app/dist.zip package.json
```



Now this has two problems:
  * We have to manually build the ember application and upload it to the github releases url.
  * Builds are not deterministic since each person has their own **node**, **npm**, **bower** & **ember-cli** combination. This has already accounted for some time lost looking on why seemingly identic builds some failed and some not.


## The Solution

The solution came by using a combination of two new approaches:

  1. Using a docker image with **node**,**npm**, **bower** & **ember-cli** installed, therefore guaranteeing that every build would be with the same versions.
  2. Using **Docker**'s [multi-stage builds](https://docs.docker.com/engine/userguide/eng-image/multistage-build/#use-multi-stage-builds). Simply put, it allows to use the output of a given image as the input of the next one , avoiding fat images and simplifying the building process.


The first part is achieved by using the [docker-ember](https://github.com/madnificent/docker-ember) image, ensuring fixed versions for the build tools:

```
FROM ubuntu:16.04
MAINTAINER Aad Versteden <madnificent@gmail.com>

# Install nodejs as per http://askubuntu.com/questions/672994/how-to-install-nodejs-4-on-ubuntu-15-04-64-bit-edition
RUN apt-get -y update; apt-get -y install wget python build-essential git libfontconfig
RUN wget -qO- https://deb.nodesource.com/setup_7.x > node_setup.sh
RUN bash node_setup.sh
RUN apt-get -y install nodejs
RUN npm install -g bower@1.7.9
RUN echo '{ "allow_root": true }' > /root/.bowerrc
RUN npm install -g ember-cli@2.14.0

WORKDIR /app
```


The second part is achieved by using the multi-stage build in the process, building the ember app and copying the resulting *dist* output folder inside **nginx**'s serving directory.


```
FROM madnificent/ember:2.14.0 as ember

MAINTAINER Esteban Sastre <esteban.sastre@tenforce.com>

COPY . /app
RUN npm install && bower install
RUN ember build

FROM semtech/mu-nginx-spa-proxy
COPY --from=ember /app/dist /app
```

This way, all the building process is limited to a simple `docker build .`


Have fun!
