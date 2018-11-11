---
ID: 2382
post_title: 'Docker &#8211; An apache &#8216;hello world&#8217; example'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/docker/docker-an-apache-hello-world-example
published: true
post_date: 2017-10-15 12:09:06
---
Here we're going to build a simple docker container that is running apache web server and then access it from your laptop's web browser. 


We will be building our container using the official ubuntu docker image. 

First off, pull down the ubuntu image, by running:

<pre>
$ docker pull ubuntu
Using default tag: latest
latest: Pulling from library/ubuntu
ae79f2514705: Pull complete
5ad56d5fc149: Pull complete
170e558760e8: Pull complete
395460e233f5: Pull complete
6f01dc62e444: Pull complete
Digest: sha256:506e2d5852de1d7c90d538c5332bd3cc33b9cbd26f6ca653875899c505c82687
Status: Downloaded newer image for ubuntu:latest
</pre> 

Then confirm that you have this image now:

<pre>
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              747cb2d60bbe        4 days ago          122MB
</pre>


Next we need to create a new container which runs the bash terminal in interactive mode. We need to expose port 80 for this container, then we need to map one of our host's port (e.g. 8080) to the new exposed container port 80.


<pre>
$ docker run -p 8080:80 -it ubuntu /bin/bash
root@f5842a8d0c33:/#
</pre>

Note: I'm using a ubuntu image for this example. I normally prefer to use centos, but I ran into a weird bug when using the centos image, so used ubuntu. 

I'm now inside this container as indicated above. If you want to exit the container, you simply do:

<pre>
root@f5842a8d0c33:/# exit
exit
</pre>

However that ends up stopping your container altogether:

<pre>
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
</pre>

To start up this particular container, we first find it's id:

<pre>
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
f5842a8d0c33        ubuntu              "/bin/bash"         3 minutes ago       Exited (0) 2 minutes ago                       naughty_shockley
</pre>

Then start it by running:


<pre>
$ docker start f5842a8d0c33
f5842a8d0c33
</pre>



Now confirm that your container is running:

<pre>
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                  NAMES
f5842a8d0c33        ubuntu              "/bin/bash"         11 minutes ago      Up 4 minutes        0.0.0.0:8080->80/tcp   naughty_shockley
</pre>


Notice here, that the portwording is also defined. 


Now let's log into our docker container:


<pre>
$ docker exec -it f5842a8d0c33 /bin/bash
root@f5842a8d0c33:/#
</pre>


Next we do an apt-get update:


<pre>
root@f5842a8d0c33:/# apt-get update
Get:1 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]
Get:2 http://archive.ubuntu.com/ubuntu xenial InRelease [247 kB]
Get:3 http://security.ubuntu.com/ubuntu xenial-security/universe Sources [50.1 kB]
Get:4 http://archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]
Get:5 http://archive.ubuntu.com/ubuntu xenial-backports InRelease [102 kB]
Get:6 http://archive.ubuntu.com/ubuntu xenial/universe Sources [9802 kB]
Get:7 http://security.ubuntu.com/ubuntu xenial-security/main amd64 Packages [471 kB]
Get:8 http://security.ubuntu.com/ubuntu xenial-security/restricted amd64 Packages [12.8 kB]
Get:9 http://security.ubuntu.com/ubuntu xenial-security/universe amd64 Packages [219 kB]
Get:10 http://security.ubuntu.com/ubuntu xenial-security/multiverse amd64 Packages [2930 B]
Get:11 http://archive.ubuntu.com/ubuntu xenial/main amd64 Packages [1558 kB]
Get:12 http://archive.ubuntu.com/ubuntu xenial/restricted amd64 Packages [14.1 kB]
Get:13 http://archive.ubuntu.com/ubuntu xenial/universe amd64 Packages [9827 kB]
Get:14 http://archive.ubuntu.com/ubuntu xenial/multiverse amd64 Packages [176 kB]
Get:15 http://archive.ubuntu.com/ubuntu xenial-updates/universe Sources [221 kB]
Get:16 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 Packages [831 kB]
Get:17 http://archive.ubuntu.com/ubuntu xenial-updates/restricted amd64 Packages [13.5 kB]
Get:18 http://archive.ubuntu.com/ubuntu xenial-updates/universe amd64 Packages [691 kB]
Get:19 http://archive.ubuntu.com/ubuntu xenial-updates/multiverse amd64 Packages [17.5 kB]
Get:20 http://archive.ubuntu.com/ubuntu xenial-backports/main amd64 Packages [5176 B]
Get:21 http://archive.ubuntu.com/ubuntu xenial-backports/universe amd64 Packages [6354 B]
Fetched 24.5 MB in 17s (1408 kB/s)
Reading package lists... Done
</pre>


Now install apache2:



<pre>
root@f5842a8d0c33:/# apt-get install apache2 -y
</pre>


Now start apache:

<pre>
$ root@f5842a8d0c33:/# apachectl start
AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 172.17.0.2. Set the 'ServerName' directive globally to suppress this message
</pre>

Also enable the service:

<pre>
root@f5842a8d0c33:/# systemctl enable apache2
apache2.service is not a native service, redirecting to systemd-sysv-install
Executing /lib/systemd/systemd-sysv-install enable apache2
</pre>



Next go to your laptop's web browser and open http://localhost:8080/. 

You should see your an ubuntu/apache welcome page. 



This is a quick and dirty way to get off the ground with this work.