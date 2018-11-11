---
ID: 478
post_title: Job Scheduling
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-job-scheduling
published: true
post_date: 2015-11-03 00:00:00
---
There will be times when you will want to schedule some task to automatically occur in the future. There are 2 possible scenrario. 

<strong>Scenario 1</strong> - In the first scenario you might want to run tasks periodically, e.g. once a month. In which case the best way to achieve is by setting up a cron job. 

<strong>Scenario 2</strong> - The second scenario is that you want to schedule a one-off task to take place at some point in the future, e.g. perform a patch installation at 2am on early Sunday morning. The best solution to achieve this is by using the "at" utility.

In the next few articles we will cover cron (in its various forms) as well as the "at" utility.