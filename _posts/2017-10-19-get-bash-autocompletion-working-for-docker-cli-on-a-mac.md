---
ID: 2388
post_title: >
  Get bash autocompletion working for
  docker cli on a mac
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/docker/get-bash-autocompletion-working-for-docker-cli-on-a-mac
published: true
post_date: 2017-10-19 16:57:07
---
First install the following formulas:

<pre>
$ brew install bash-completion
$ brew cask install docker
</pre>

Next via the gui launcher, find the docker icon and launch it, then follow the prompts. 

then restart your bash terminal. 

Now run the following command to create a few symbolic links:

<pre>
ln -s /Applications/Docker.app/Contents/Resources/etc/docker.bash-completion /usr/local/etc/bash_completion.d/docker
ln -s /Applications/Docker.app/Contents/Resources/etc/docker-machine.bash-completion /usr/local/etc/bash_completion.d/docker-machine
ln -s /Applications/Docker.app/Contents/Resources/etc/docker-compose.bash-completion /usr/local/etc/bash_completion.d/docker-compose

</pre>

Then restart the bash terminal. 


Therefore running the above curl commands resulted in the following files being created:

<pre>
$ pwd
/usr/local/etc/bash_completion.d
$ cd /usr/local/etc/bash_completion.d/
$ ll | grep docker
-rw-r--r--  1 schowdhury  admin     15 19 Oct 11:18 docker
-rw-r--r--  1 schowdhury  admin     15 19 Oct 11:18 docker-compose
-rw-r--r--  1 schowdhury  admin  10347 19 Oct 11:18 docker-machine
-rw-r--r--  1 schowdhury  admin   1469 19 Oct 11:19 docker-machine-prompt
-rw-r--r--  1 schowdhury  admin   1525 19 Oct 11:18 docker-machine-wrapper

</pre>

References:
<a href="https://webascrazy.net/2017/02/02/add-docker-autocompletion-in-your-shell/" rel="nofollow">https://webascrazy.net/2017/02/02/add-docker-autocompletion-in-your-shell/</a>
<a href="https://docs.docker.com/docker-for-mac/#installing-bash-completion" rel="nofollow">https://docs.docker.com/docker-for-mac/#installing-bash-completion</a>





Then restart your terminal