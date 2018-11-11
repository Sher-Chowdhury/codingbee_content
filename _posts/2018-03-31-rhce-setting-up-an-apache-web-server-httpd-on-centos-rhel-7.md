---
ID: 3358
post_title: >
  RHCE – Setting up an Apache Web Server
  (httpd) on CentOS/RHEL 7
author: sher
post_excerpt: >
  In this series of articles we’re going
  to set up an Apache Web Server and
  walkthrough the various Apache
  configurations and features. To start
  with this article will cover setting up
  a basic Apache server with the default
  out-of-the-box apache configurations.
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/rhce-setting-up-an-apache-web-server-httpd-on-centos-rhel-7
published: true
post_date: 2018-03-31 13:05:58
---
[post-content id=2754]

[post-content post_name=rhsca-quiz] 

[accordion]
[toggle title="What is the command to install Apache web server?"]
$ yum group install 'Basic Web Server'
[/toggle]
[toggle title="What is the commadn to install a few text based web browsers?"]
$ yum install elinks lynx
[/toggle]
[toggle title="What is the command to start+enable the apache service?"]
$ systemctl start httpd
$ systemctl enable httpd
[/toggle]
[toggle title="What is the command to open up the firewalls?"]
$ firewall-cmd --add-service=http
$ firewall-cmd --add-service=https
$ systemctl restart firewalld
[/toggle]
[/accordion]