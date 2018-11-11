---
ID: 381
post_title: 'RHCSA &#8211; Scheduling jobs with cron'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-scheduling-jobs-with-cron
published: true
post_date: 2015-05-04 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to check the status of the cron's service?"]
$ systemctl status crond
[/toggle]
[toggle title="What are the 3 main ways to create cron jobs?"]
- Add an entry to the <code>/etc/crontab</code> file or drop files in <code>/etc/cron.d</code>.
- Using the <code>crontab -e</code> command.
- By dropping shell scripts into the <code>/etc/cron.hourly</code>, <code>/etc/cron.daily</code>, <code>/etc/cron.weekly</code>, or <code>/etc/cron.monthly</code> directories. This is via "anacron".
[/toggle]
[toggle title="What is the /etc/crontab entry to run the 'ls' command at 5.01am (min & hour), of 3rd (day of the month) of (every) April, of any day (i.e. doesn't mater whether it is mon-sun), every year.   It doesn’t matter if the day is Monday, Tuesday,… etc. This job is run under the username of 'david'. The output is also piped to /tmp/cron.log?"]
1 5 3 4 * david ls >> /tmp/cron.log    
[/toggle]
[toggle title="Same as the previous answer, but this time runs ls, at 5.01am, 6.01am, 7.01am…..10.01am. Hence runs the command several times, during a 5-hour window, on that particular day every year. Also the command is run by under the context of a user called 'jenny'?"]
1 5-10 3 4 * ls >> jenny /tmp/cron.log
[/toggle]
[toggle title="Same as the first /etc/crontab entry's answer, but this time runs 3 times within the hour, 5.00am, 5.17am, and 5.48am. Also run this as the root user?"]
0,17,48 5 3 4 * ls >> /tmp/cron.log  
[/toggle]
[toggle title="Same as the first /etc/crontab entry's answer, but runs 6 times within the hour, 5.00am, 5.10am, 5.20am, 5.30am, 5.40am, and 5.50am?"]
0,10,20,30,40,50 5 3 4 * david ls  >> /tmp/cron.log  
[/toggle]
[toggle title="Same as the previous example but written in shorthand form?"]
*/10 5 3 4 * david ls  >> /tmp/cron.log  
[/toggle]
[toggle title="Same as previous example, but this time the cron job is run every 3 days. This means that it will run on the following days?"]
*/10 5 */3 4 * david ls >> /tmp/cron.log
[/toggle]
[toggle title="Same as the first /etc/crontab, but this time run the job at 5 mins past midnight every Sunday?"]
5 0 * * Sunday david ls >> /tmp/cron.log    
[/toggle]
[toggle title="In which directory can you drop in custom crontab files so that they automatically get picked up by the crond daemon?"]
/etc/cron.d
[/toggle]
[toggle title="What can the custom cron files be called?"]
There is no naming convention, you can call them anything you want. 
[/toggle]
[toggle title="What is the command to create a cron job straight from the command line, in a vim-like editor (also the cron has to be run as the user 'david')?"]
$ su - david
$ crontab -e   
[/toggle]
[toggle title="The above comamnd creates a file storing the cron job. Where is this file located?"]
/var/spool/cron
[/toggle]
[toggle title="What will this file be called?"]
It will be named after the user who ran the crontab command. 
[/toggle]
[/accordion]

<hr/>




There will be times when you will want to schedule some task to automatically occur in the future. There are 2 solutions that are available that let's you do this "at", and "cron". The "at" utility is designed for scheduling an adhoc job, e.g. perform a patch installation at 9pm on Sunday. Whereas the "cron" utility is designed for scheduling a job that needs to be repeated on a regular basis, e.g. take backups at 5am every morning.


<h2>The crond service</h2>
If you want to run a task at regular intervals, then you need to use cron solution. The cron solution relies on the crond service in order for it to work:


<pre>
$ systemctl status crond
crond.service - Command Scheduler
   Loaded: loaded (/usr/lib/systemd/system/crond.service; enabled)
   Active: active (running) since Wed 2015-05-06 20:32:44 BST; 56min ago
 Main PID: 1343 (crond)
   CGroup: /system.slice/crond.service
           └─1343 /usr/sbin/crond -n

May 06 20:32:44 localhost.localdomain systemd[1]: Started Command Scheduler.
May 06 20:32:44 localhost.localdomain crond[1343]: (CRON) INFO (RANDOM_DELAY will be scaled with factor 31% if used.)
May 06 20:32:46 localhost.localdomain crond[1343]: (CRON) INFO (running with inotify support)


</pre>

The crond service is running by default and is actually used by other services too. 



There are 3 ways to create cron jobs:

<ol>
	<li>Add an entry to the <code>/etc/crontab</code> file.</li>
	<li>Using the <code>crontab -e</code> command. Note, in case you're wondering, then is no such command called "cron".</li>
	<li>By dropping shell scripts into the <code>/etc/cron.hourly</code>, <code>/etc/cron.daily</code>, <code>/etc/cron.weekly</code>, or <code>/etc/cron.monthly</code> directories. This is via <a href="http://codingbee.net/tutorials/rhcsa/rhcsa-scheduling-jobs-with-anacron/">anacron</a>.</li>
</ol>

Let's take a look at each approach in turn:

<h2>Adding a cron job via the /etc/crontab file</h2>

You can add your cron job by adding a line to the following:


<pre>
$ cat /etc/crontab
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root

# For details see man 4 crontabs

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed

</pre>



While you can add you cron jobs here, in practice this file is mainly used for defining various environment variables to assist with the running of the cron jobs, using the other ways. The first few lines of this file defines a number of env variables, which will help automated tasks to run properly, they are:

<ul>
	<li><strong>SHELL</strong> - shell environment used for running jobs (in the example, the Bash shell)</li>
	<li><strong>PATH</strong>  - paths to executable programs</li>
	<li><strong>MAILTO</strong>  - username of the user who receives the output of the cron jobs by email (reporting on jobs that passed/failed). If the MAILTO variable is not defined (MAILTO=), then email is not sent.</li>
</ul>

Note, to help you understand, you can use this online <a href="https://cronwtf.com/">cron job checker</a> which describes cron jobs in a more human readable format.

Here are some cron entry examples:

<strong>Example 1</strong>
<pre>
1 5 3 4 * david ls >> /tmp/cron.log    
</pre>                     

This runs the ls command (and pipes the output to /tmp/cron.log) at 5.01am (min=1 & hour=5), of 3rd (day of the month) of April (month=4), of any day (i.e. doesn't mater whether it is mon-sun, as indicated by the "*"), every year (this is always assumed for cron jobs).. This job is run under the username of "david". 


<strong>Example 2</strong>


<pre>1 5-10 3 4 * ls >> jenny /tmp/cron.log</pre>                            

This is the same as the 1st example, but runs ls, at 5.01am, 6.01am, 7.01am…..10.01am. Hence runs the command several times, during a 5-hour window, on that particular day every year. Also this time the job is run as the user "jenny".

Note: you can think of the asterisk to mean "every". Hence if the hour setting is represented by "*" then it is actually equivalent to "0-24". Similarly an asterisk for the minute setting represents "0-60"  

<strong>Example 3</strong>


<pre>
0,17,48 5 3 4 * ls >> /tmp/cron.log  
</pre>  


This is the same as the 1st example, but runs 3 times within the hour, 5.00am, 5.17am, and 5.48am. Also note that we have omitted the username, by default this job will be run the context of the root user. 



<strong>Example 4</strong>

<pre>
0,10,20,30,40,50 5 3 4 * david ls  >> /tmp/cron.log  
</pre>  

This is the same as the 1st example, but runs 6 times within the hour, 5.00am, 5.10am, 5.20am, 5.30am, 5.40am, and 5.50am.



<strong>Example 5</strong>

<pre>
*/10 5 3 4 * david ls >> /tmp/cron.log
</pre>

Shorthand way of writing the above command (basically any number divisible by 10, without leaving a remainder).



<strong>Example 6</strong>

<pre>
*/10 5 */3 4 * david ls >> /tmp/cron.log
</pre>

Same as previous example, but this time the cron job is run every 3 days. This means that it will run on the following days. 

1,4,7,10,13,16,19,22,25,28,31  (assuming there are 31 days in the given month)

In this particular scenario, the job won't always run every 3 days. That's because due to the way cron works internally, cron always start job runs from the first day of the month (when there is an asterisk in the day setting). So on a 31 day month, it will run on day 31, and then the very next day, since that is the first day of the new month again. However it will work as expected when going from a 30 day month to a 31 day month.   

You will get a similar situation with the minute setting too, if you choose an "every" number that is not divisible by 60. In the previous example we used "*/10", so it was ok, but if we did something like "*/11" (which is every 11 minutes), then we'll get this issue from rolling from one hour to the next. 

The same applies for the hour and month settings too. 


<strong>Example 7</strong>
<pre>
5 0 * * Sunday david ls >> /tmp/cron.log    
</pre>                     

This runs the job at 5 mins past midnight every Sunday.

It also has "every day", but "Sunday" over-rides this.    



<h2>Adding cron jobs to /etc/cron.d</h2>

Defining cron jobs in /etc/crontab is no longer the recommended approach. That's because over time you could end up adding lots of cron jobs to a single file, which would make it unwieldy. Also if one of cron jobs has a syntax error then it could cause all the other cron jobs from running.  


The better apprach is to use /etc/crontab file as a template to create your own crontab file. You then place your custom crontab file in the <code>/etc/cron.d</code> so that it get's picked up by the crond daemon. 


<pre>
$ ls -l /etc/cron.d
total 16
-rw-r--r--. 1 root root 128 Jul 30  2014 0hourly
-rw-r--r--. 1 root root 108 Mar  6 02:12 raid-check
-rw-------. 1 root root 235 Mar  6 05:45 sysstat
-rw-r--r--. 1 root root 187 Jan 27  2014 unbound-anchor

$ cat /etc/cron.d/sysstat
# Run system activity accounting tool every 10 minutes
*/10 * * * * root /usr/lib64/sa/sa1 1 1
# 0 * * * * root /usr/lib64/sa/sa1 600 6 &
# Generate a daily summary of process accounting at 23:53
53 23 * * * root /usr/lib64/sa/sa2 -A
</pre>

The files you place in this directory can be called anything you want. 

This is possible way for an application (e.g. firefox) to check for updates. If there is new version of Firefox then Firefox would then prompt the user to do an upgrade to the latest version. To make this possible, the firefox custom cron file would get dropped into /etc/cron.d during the installation of firefox. 




<h2>Using the "crontab" command</h2>

The crontab command (short for cron tables) is the recommended way to add/edit/delete cron jobs, that are to be run under the uid of a particular user:

<pre>
$ crontab -h
crontab: invalid option -- 'h'
crontab: usage error: unrecognized option
Usage:
 crontab [options] file
 crontab [options]
 crontab -n [hostname]

Options:
 -u <user>  define user
 -e         edit user's crontab
 -l         list user's crontab
 -r         delete user's crontab
 -i         prompt before deleting
 -n <host>  set host in cluster to run users' crontabs
 -c         get host in cluster to run users' crontabs
 -s         selinux context
 -x <mask>  enable debugging

Default operation is replace, per 1003.2


</pre>


The crond service is the service that reads these tables and performs the scheduled tasks. You can create/edit/delete your cron jobs by running the following command:

<pre>
$ crontab -e   
</pre>

Running this command will result in a vim like editor opening up where you write/edit/delete your cron jobs. These cron jobs will run under the id of the user who ran the <code>crontab -e</code> command. Hence if you want to run a cron job as a certain user, then you need to first su into that user before running the above command.     

After saving your changes and exiting the vim-like editor, behind the scenes a new file (named after the user) gets generated/updated in the following directory:

<pre>
/var/spool/cron
</pre>   


When you create a new cron job, you are effectively creating/updating a file that stores cron jobs (for a given user). These "cron files" are stored in the following directory:


These cron files are named after the user that the cron job will run under. For example, cron jobs created while logged in as the root user are stored in the following file:

<pre>
$ ls -l /var/spool/cron/root
-rw-------. 1 root root 59 May  6 22:48 /var/spool/cron/root
[root@localhost ~]# cat /var/spool/cron/root
*  *  *  *  * echo "`date` - hello world" >> /tmp/cron.log
</pre>

Note: The files in the /var/spool/cron folder are not supposed to be created/edited directly, they are only supposed to be created/edited/deleted via the crontab command.  


Once you have created your cron jobs, you can view them by viewing the files in /var/spool/cron directly, or using the crontab's "l" option:


<pre>
$ crontab -l
*  *  *  *  * echo "`date` - hello world" >> /tmp/cron.log
*/10 5 3 4 * ls     >> /tmp/cron.log
</pre>


If you are the root user, and you want to edit another user’s cron-tables, then instead of switching users (via su command) you can use the crontab's "u" option:

<pre>
$ id
uid=0(root) gid=0(root) groups=0(root) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
$ grep sher /etc/passwd
sher:x:1002:1002::/home/sher:/bin/bash
$ ls -l /var/spool/cron | grep sher
$ crontab -eu sher                        # opens vim mode to write in the cron job
no crontab for sher - using an empty one
crontab: installing new crontab
[root@localhost ~]# crontab -lu sher
*  *  *  *  * echo "`date` - hello world" >> /tmp/cron.log
[root@localhost ~]# ls -l /var/spool/cron | grep sher
-rw-------. 1 root root 59 May  9 21:39 sher
$ cat /var/spool/cron/sher
*  *  *  *  * echo "`date` - hello world" >> /tmp/cron.log

</pre>       



 




<strong>See also:</strong>

<a href="http://www.centos.org/docs/2/rhl-cg-en-7.2/autotasks.html">http://www.centos.org/docs/2/rhl-cg-en-7.2/autotasks.html</a>

<a href="https://access.redhat.com/knowledge/docs/en-US/Red_Hat_Enterprise_Linux/3/html/System_Administration_Guide/ch-autotasks.html">https://access.redhat.com/knowledge/docs/en-US/Red_Hat_Enterprise_Linux/3/html/System_Administration_Guide/ch-autotasks.html</a>

<a href="https://access.redhat.com/knowledge/docs/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/ch-Automating_System_Tasks.html">https://access.redhat.com/knowledge/docs/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/ch-Automating_System_Tasks.html</a>

<a href="http://www.thegeekstuff.com/2009/06/15-practical-crontab-examples/">http://www.thegeekstuff.com/2009/06/15-practical-crontab-examples/</a>

<a href="http://www.thegeekstuff.com/2011/07/cron-every-5-minutes/">http://www.thegeekstuff.com/2011/07/cron-every-5-minutes/</a>

<a href="http://www.thegeekstuff.com/2011/12/crontab-command/">http://www.thegeekstuff.com/2011/12/crontab-command/</a>

<a href="http://www.cyberciti.biz/faq/linux-when-does-cron-daily-weekly-monthly-run/">http://www.cyberciti.biz/faq/linux-when-does-cron-daily-weekly-monthly-run/</a>

<a href="http://www.howtogeek.com/101288/how-to-schedule-tasks-on-linux-an-introduction-to-crontab-files/">http://www.howtogeek.com/101288/how-to-schedule-tasks-on-linux-an-introduction-to-crontab-files/</a>

<a href="http://helpdeskgeek.com/linux-tips/crontab-howto-tutorial-syntax/">http://helpdeskgeek.com/linux-tips/crontab-howto-tutorial-syntax/</a>

<a href="http://www.unixgeeks.org/security/newbie/unix/cron-1.html">http://www.unixgeeks.org/security/newbie/unix/cron-1.html</a>

<a href="http://www.cyberciti.biz/faq/linux-show-what-cron-jobs-are-setup/">http://www.cyberciti.biz/faq/linux-show-what-cron-jobs-are-setup/</a>

https://cronwtf.com/