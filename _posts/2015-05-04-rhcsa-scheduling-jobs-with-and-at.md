---
ID: 383
post_title: 'RHCSA &#8211; Scheduling jobs with and &#8220;at&#8221;'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-scheduling-jobs-with-and-at
published: true
post_date: 2015-05-04 00:00:00
---
There will be times when you will want to schedule some task to automatically occur in the future. There are 2 solutions that are available that let's you do this "at", and "cron". The "at" utility is designed for scheduling an adhoc job, e.g. perform a patch installation at 9pm on Sunday. Whereas the "cron" utility is designed for scheduling a job that needs to be repeated on a regular basis, e.g. take backups at 5am every morning.

The next couple of lessons will cover both "cron" and "at". 



<h2>Using "at"</h2>
If you want to run a one-off job at some point in the future, then you need to use "at" command. 

"at" is comprised of the atd service:


<pre>
$ systemctl status atd
atd.service - Job spooling tools
   Loaded: loaded (/usr/lib/systemd/system/atd.service; enabled)
   Active: active (running) since Wed 2015-05-06 20:32:44 BST; 1h 3min ago
 Main PID: 1344 (atd)
   CGroup: /system.slice/atd.service
           └─1344 /usr/sbin/atd -f

May 06 20:32:44 localhost.localdomain systemd[1]: Started Job spooling tools.

</pre>

To create a new "at job", you use the "at" command:


<pre>
$  whatis at
at (1)               - queue, examine or delete jobs for later execution
</pre>