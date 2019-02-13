---
ID: 54
post_title: rhcsa-the-bash-terminal
author: admin
post_excerpt: ""
layout: post
permalink: http://localhost:8000/?p=54
published: true
post_date: 2014-05-04 00:00:00
---
In Linux, nearly all day to day tasks are performed inside a command line terminal, aka the bash terminal. 

There's a few ways to access it depending on various circumstance. 

First of it depends on whether you are sitting in front of your Linux machine or you want to access it remotely (e.g. this is likely if your machine is locked away in data centre, or server room). 

## Directly Accessing your Bash terminal

If you are sitting in front of your machine, then to access the terminal will depend on whether your machine has a graphical User Interface installed. If so then you can access the bash terminal by 

If your machine has the graphical UI, then you can access your bash terminal by either:

<pre>right click anywhere on the desktop -> click "open in terminal"  </pre>

If you machine doesn't have a UI installed then you are automatically presented with the bash terminal when your machine boots up. 

## Remotely accessing the Bash terminal

If the machine you want to access is behind closed doors e.g. inside a data centre, server room, or in the cloud, then you can access the machine remotely from your local desktop. 

### Remotely accessing a machine from a Linux Desktop

If your local desktop is a Linux machine of any distro (e.g. CentOS, Ubunto, Debian...etc.), Then you can access your remote server's bash terminal by establishing a remote session using the SSH protocol. SSH is a universal Linux protocol that is the secure remote connection of choice across all Linux and Unix distros. 

Using SSH is a bit like creating a secure tunnel between your machine and the remote machine. Once the SSH session has been established, any commands or tasks you perform are actually performed on the remote machine. We establish an ssh session using the ssh command. For example, if our local machine's fqdn is "puppetmaster.local" and we want to use this machine to connect as the "vagrant" user to a remote machine which has the name "puppetagent01.local" then we run the command:

<pre>[root@puppetmaster ~]$ ssh vagrant@puppetagent01.local
vagrant@puppetagent01.local's password:
[vagrant@puppetagent01 ~]$
</pre>

### Remotely accessing a machine from Windows Desktop

Windows doesn't natively support ssh. Therefore tyou need to install an ssh client on your windows desktop machine in order to connect to a remote Linux machine. The most popular free ssh client is <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" rel="nofollow">PuTTY</a>.