---
ID: 2390
post_title: Install Docker for Mac using homebrew
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/docker/install-docker-for-mac-using-homebrew
published: true
post_date: 2017-10-19 17:31:04
---
Here's the command I ran:

<pre>
brew install bash-completion
brew cask install docker
brew install kubectl
brew cask install minikube
</pre>

Then go to the gui launcher and start up docker, and follow the prompts. 

Then open a terminal and you should fine the following cli tools installed. 

<pre>
$ docker version
Docker version 17.09.0-ce, build afdb6d4

$ docker-compose version
docker-compose version 1.16.1, build 6d1ac21

$ docker-machine --version
docker-machine version 0.12.2, build 9371605

$ kubectl version --client
Client Version: version.Info{Major:"1", Minor:"6", GitVersion:"v1.6.2", GitCommit:"477efc3cbe6a7effca06bd1452fa356e2201e1ee", GitTreeState:"clean", BuildDate:"2017-04-19T20:33:11Z", GoVersion:"go1.7.5", Compiler:"gc", Platform:"darwin/amd64"}
</pre>

Reference
<a href="https://docs.docker.com/docker-for-mac/install" rel="nofollow">https://docs.docker.com/docker-for-mac/install</a>