---
ID: 310
post_title: 'Jenkins &#8211; Automate the creation of new Jenkins jobs'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/jenkins-automate-the-creation-of-new-jenkins-jobs
published: true
post_date: 2015-03-06 00:00:00
---
It is possible to create a jenkins job that when run:

- scans git for any newly created repo. 
- creates a new jenkins job for new repos. 


This is achieved using the the following plugin:

https://wiki.jenkins-ci.org/display/JENKINS/Job+DSL+Plugin

as well as writing some groovy script. 

This jenkins job can then be run as an hourly cron job, or manually, as and when needed. 

Also see:


https://github.com/jenkinsci/job-dsl-plugin/wiki/Tutorial---Using-the-Jenkins-Job-DSL

https://wiki.jenkins-ci.org/display/JENKINS/Jenkins+CLI