---
ID: 449
post_title: Scheduling jobs with Anacron
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-scheduling-jobs-with-anacron
published: true
post_date: 2015-09-19 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to install the anacron system's package?"]
$ yum install cronie-anacron
[/toggle]
[toggle title="If you have a shell script thay you want to run daily, but you don't mind exactly when that script is run (e.g. beginning, middle, or end of the day) as long as it is run sometime within that day, then what directory do you place this file in?"]
/etc/cron.daily
[/toggle]
[toggle title="What is the anacron's config file?"]
/etc/anacrontab
[/toggle]
[toggle title="What are the full paths to all the anacron timestamps files?"]
/var/spool/anacron/cron.daily
/var/spool/anacron/cron.monthly
/var/spool/anacron/cron.weekly
[/toggle]
[toggle title="Which cron file contains the cron that manages the hourly anacron system?"]
$ cat /etc/cron.d/0hourly
[/toggle]
[/accordion]

<hr/>



If you have a shell script thay you want to run daily, but you don't mind exactly when that script is run (e.g. beginning, middle, or end of the day) as long as it is run sometime within that day, then you can achieve this by simply dropping that script into the <code>/etc/cron.daily</code> folder. You then leave it to crond service to pick a convenient time to run that script.

There's a few equivalent folders for setting up hourly, weekly, and monthly jobs. In total we have four folders: 
   

<pre>
$ ls -l /etc | grep cron
-rw-------.  1 root root      541 Jul 30  2014 anacrontab
drwxr-xr-x.  2 root root       72 Apr  3 16:31 cron.d
drwxr-xr-x.  2 root root       76 Apr  3 16:24 cron.daily     # this one
-rw-------.  1 root root        0 Jul 30  2014 cron.deny      
drwxr-xr-x.  2 root root       44 Apr  3 16:24 cron.hourly    # this one 
drwxr-xr-x.  2 root root        6 Jun  9  2014 cron.monthly   # this one
-rw-r--r--.  1 root root      451 Jun  9  2014 crontab
drwxr-xr-x.  2 root root        6 Jun  9  2014 cron.weekly    # this one
</pre>


In practice, scripts are dropped into these folders when installing various rpm packages that require the use of cron for it to function. Don't forget to give these scripts execute permissions in order for them to run. 


This approach to setting cron jobs is provided by the cronie-anacron (aka anacron) package:

<pre>
$ rpm -ql cronie-anacron
/etc/anacrontab
/etc/cron.hourly/0anacron
/usr/sbin/anacron
/usr/share/man/man5/anacrontab.5.gz
/usr/share/man/man8/anacron.8.gz
/var/spool/anacron
/var/spool/anacron/cron.daily
/var/spool/anacron/cron.monthly
/var/spool/anacron/cron.weekly
</pre>



<h2>The Anacrontab config file</h2>

Let's say that you have RHEL vm that has been left switched on for the whole week, and you only switch it on at around 10am on Sunday morning (i.e. last day of the week). Now let's say you have jobs in the cron.weekly folder. Then will anacron realise that these jobs needs to be run as matter of urgency since the machine has been switched-off for nearly the whole week? The the cool thing with anacron, is that it keeps track of the last time each anacron job has been run (we'll show you where this is stored further down). After that anacron will determine whether it has enough time left in the current window (which in this case is the week) to run the job, and if so then it starts the job.     

If you want to make adjustments to this behavior, then you need edit the <code>/etc/anacrontab</code> config file:

<pre>
$  cat /etc/anacrontab
# /etc/anacrontab: configuration file for anacron

# See anacron(8) and anacrontab(5) for details.

SHELL=/bin/sh
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
# the maximal random delay added to the base delay of the jobs
RANDOM_DELAY=45
# the jobs will be started during the following hours only
START_HOURS_RANGE=3-22

#period in days   delay in minutes      job-identifier          command
1                  5                    cron.daily              nice run-parts /etc/cron.daily
7                 25                    cron.weekly             nice run-parts /etc/cron.weekly
@monthly          45                    cron.monthly            nice run-parts /etc/cron.monthly

</pre>

Note: the hour.daily entry is missing here. We'll explain why that is later. 

Note: The "delay in minutes" column is to do with the minimum number of minutes of delay in minutes to run the job, after the machine is booted up. 

This "delay in minutes" combined with "RANDOM_DELAY" is designed to stagger out the anacron job so to avoid all the anacron jobs getting triggered at once when the machine is booted up.   

The "job-identifier" is just a name given to particular job. 

The "command" is the command to run. In the above example we prefixed each command by "<a href="http://codingbee.net/tutorials/rhcsa/rhcsa-setting-process-priorities-by-setting-nice-values/">nice</a>". The actual command that is run is "run-parts". run-parts is a simple utility that runs all the scripts in the given directory. 

What if you want to run a cron job once in an any time 3 day period, then you can create your own custom anacron job entry in this file:



<pre>
#period in days   delay in minutes      job-identifier          command
4                 0                     myscript.sh             /path/to/myscript.sh
</pre>
  



Now as mentioned earlier, anacron keep track of last job run time stamps, you can find them here:


<pre>
$ ls -l /var/spool/anacron/
total 12
-rw-------. 1 root root 9 Sep 20 09:18 cron.daily
-rw-------. 1 root root 9 Sep 19 23:09 cron.monthly
-rw-------. 1 root root 9 Sep 19 22:49 cron.weekly


$ date
Sun 20 Sep 11:00:52 BST 2015
$ cat /var/spool/anacron/cron.weekly
20150919                                  # YYYYMMDD
</pre>



As you saw earlier, the "cron.hourly" entry was missing from the anacrontab file. That's because the hourly jobs are handled differently due to the limitations of how anacron works. They are instead handled as part of a normal cron:

<pre>
$ cat /etc/cron.d/0hourly
# Run the hourly jobs
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
01 * * * * root run-parts /etc/cron.hourly
</pre>

As you can see, hourly cron jobs are run on the first minute of every hour. 






See also: http://www.thegeekstuff.com/2011/05/anacron-examples/ 










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