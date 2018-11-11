---
ID: 2627
post_title: >
  System monitoring and reporting using
  sysstat and dstat on CentOS/RHEL 7
author: sher
post_excerpt: "It's often useful to measure and view various system utilisation metrics. dstat and sysstat are typical tools that can do this task. "
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/system-monitoring-and-reporting-using-sysstat-and-dstat
published: true
post_date: 2018-02-28 12:14:40
---
It's often useful to measure and view various system utilisation metrics, such as for:

<ul>
	<li>cpu</li>
	<li>memory</li>
	<li>disk</li>
	<li>network</li>
</ul>


There are a lot of tools that can measure and collect these metrics, including:

<ul>
	<li>dstat - suited for creating realtime reports</li>
	<li>sysstat - suited for creating historical reports</li>
</ul>



<h2>dstat in action</h2>

Dstat shows realtime system utilisation data. First you need to install it:


<pre>
$ yum install dstat
</pre>

running dstat on it's own gives a steady stream of output until you exit out:


<pre>[root@target man]# dstat
You did not select any stats, using -cdngy by default.
----total-cpu-usage---- -dsk/total- -net/total- ---paging-- ---system--
usr sys idl wai hiq siq| read  writ| recv  send|  in   out | int   csw
  0   0 100   0   0   0|  23k   53k|   0     0 |   0     0 |  84    72
  0   0 100   0   0   0|   0     0 |  60B  338B|   0     0 |  88    82
  0   0 100   0   0   0|   0     0 |  60B  122B|   0     0 |  56    54
  0   0 100   0   0   0|   0     0 |  60B   98B|   0     0 |  53    52
  0   1 100   0   0   0|   0     0 |  60B   98B|   0     0 |  66    60
  0   0 100   0   0   0|   0     0 |  60B   98B|   0     0 |  53    55
.
.
.
...etc
</pre>

This gives a general overview. But if you want look at just the cpu stats then you use the '-c' option:

<pre>
[root@target man]# dstat -c
----total-cpu-usage----
usr sys idl wai hiq siq
  0   0 100   0   0   0
  0   0 100   0   0   0
  0   0 100   0   0   0
  0   0 100   0   0   0^C
</pre>

You take a similar approach for (d)isk, (m)emory, (n)etwork, (p)rocesses, (r)ead-write requests. 

Also you can output these stats into a csv file like this:


<pre>
$ dstat --output /tmp/dstat.csv
You did not select any stats, using -cdngy by default.
----total-cpu-usage---- -dsk/total- -net/total- ---paging-- ---system--
usr sys idl wai hiq siq| read  writ| recv  send|  in   out | int   csw
  0   0 100   0   0   0|  22k   51k|   0     0 |   0     0 |  81    70
  0   0 100   0   0   0|   0     0 |  60B  130B|   0     0 |  39    32
  0   0 100   0   0   0|   0     0 |  60B  106B|   0     0 |  21    17
  0   0 100   0   0   0|   0     0 |  60B   98B|   0     0 |  43    35 ^C
</pre>

Note it displays the output like normal as well as writing it to a file. 

Where the output file looks like this:

<pre>
cat /tmp/dstat.csv
"Dstat 0.7.2 CSV output"
"Author:","Dag Wieers <dag@wieers.com>",,,,"URL:","http://dag.wieers.com/home-made/dstat/"
"Host:","target.cb.net",,,,"User:","root"
"Cmdline:","dstat --output /tmp/dstat",,,,"Date:","28 Feb 2018 17:39:48 UTC"

"total cpu usage",,,,,,"dsk/total",,"net/total",,"paging",,"system",
"usr","sys","idl","wai","hiq","siq","read","writ","recv","send","in","out","int","csw"
0.173,0.123,99.670,0.009,0.0,0.026,22395.765,51954.426,0.0,0.0,0.0,0.0,80.816,69.846
0.0,0.0,100.0,0.0,0.0,0.0,0.0,0.0,60.0,130.0,0.0,0.0,39.0,32.0
0.0,0.0,100.0,0.0,0.0,0.0,0.0,0.0,60.0,106.0,0.0,0.0,21.0,17.0
0.0,0.0,100.0,0.0,0.0,0.0,0.0,0.0,60.0,98.0,0.0,0.0,43.0,35.0
</pre>


However this approach is quite crude, since it's hard to query this info for historical information. That's where sysstat comes to the rescue. 

<h2>sysstat in action</h2>

Sysstat is actually a collection of tools for gathering and reporting system stats. You first need to install it:


<pre>
$ yum install -y sysstat
</pre>

sysstat has a few config files and by default runs using a cron job:

<pre>
$ rpm -qc sysstat
/etc/cron.d/sysstat
/etc/sysconfig/sysstat
/etc/sysconfig/sysstat.ioconf
</pre>

Cron job can be customised by editing the <code>/etc/sysconfig/sysstat</code> file:

<pre>
$ cat /etc/sysconfig/sysstat
# sysstat-10.1.5 configuration file.

# How long to keep log files (in days).
# If value is greater than 28, then log files are kept in
# multiple directories, one for each month.
HISTORY=28

# Compress (using gzip or bzip2) sa and sar files older than (in days):
COMPRESSAFTER=31

# Parameters for the system activity data collector (see sadc manual page)
# which are used for the generation of log files.
SADC_OPTIONS="-S DISK"

# Compression program to use.
ZIP="bzip2"
</pre>

The cron job itself runs 2 tools <code>sa1</code> and <code>sa2</code>:

<pre>
$ cat /etc/cron.d/sysstat
# Run system activity accounting tool every 10 minutes
<strong>*/10 * * * * root /usr/lib64/sa/sa1 1 1</strong>
# 0 * * * * root /usr/lib64/sa/sa1 600 6 &
# Generate a daily summary of process accounting at 23:53
<strong>53 23 * * * root /usr/lib64/sa/sa2 -A</strong>
</pre>

Both of these cron jobs creates daily reports and store them in the <code>/var/log/sa</code> directory. They cron jobs creates the following files:

<ul>
	<li>sa1 generates <code>/var/log/sa/sa{dd}</code> files. It also appends to this file every 10 minutes. This file gradually grows every 10 minutes until midnight. Then a new file is generated.  </li>
	<li>sa2 generates <code>/var/log/sa/sar{dd}</code> files, once per day</li>
</ul>

Where {dd} represents the day of the month:

<pre>
$ date
Wed Feb <strong>28</strong> 18:35:15 UTC 2018
[root@target sa]# ls -lrt /var/log/sa/sa28
-rw-r--r--. 1 root root 18372 Feb 28 18:30 /var/log/sa/sa<strong>28</strong>
</pre> 

For more info about sa1 and sa2, see the man pages for these. sa1 essentially stores system statistics in special non-human-readable binary files, called '/var/log/sa/sa{dd}':


Since sadd files are not human readable, you need to use the sadf tool query the content of the sadf file. E.g. to view the data between 6pm to 7pm on the 28th of the month, then we do:

<pre>
$ [root@codingbee sa]# sadf -s 18:00:00 -e 19:00:00 /var/log/sa/sa28
codingbee.net	-1	2018-02-28 18:20:21 UTC	LINUX-RESTART
codingbee.net	600	2018-02-28 18:40:01 UTC	all	%user	0.01
codingbee.net	600	2018-02-28 18:40:01 UTC	all	%nice	0.00
codingbee.net	600	2018-02-28 18:40:01 UTC	all	%system	0.02
codingbee.net	600	2018-02-28 18:40:01 UTC	all	%iowait	0.00
codingbee.net	600	2018-02-28 18:40:01 UTC	all	%steal	0.00
codingbee.net	600	2018-02-28 18:40:01 UTC	all	%idle	99.97
codingbee.net	600	2018-02-28 18:50:01 UTC	all	%user	0.01
codingbee.net	600	2018-02-28 18:50:01 UTC	all	%nice	0.00
codingbee.net	600	2018-02-28 18:50:01 UTC	all	%system	0.02
codingbee.net	600	2018-02-28 18:50:01 UTC	all	%iowait	0.00
codingbee.net	600	2018-02-28 18:50:01 UTC	all	%steal	0.00
codingbee.net	600	2018-02-28 18:50:01 UTC	all	%idle	99.98
</pre>

sadf is actually a wrapper around another cli utility called sar (System Activity Report). Sar also comes with the sysstat package. See: 

https://www.google.co.uk/search?q=sar+command&oq=sar+command&aqs=chrome..69i57j69i60j0l4.3023j0j4&sourceid=chrome&ie=UTF-8


 


Here are some other useful commands:

uptime (prints load averages)
du 
df
free 
top
ps 
pstree
pgrep

other commands

htop
nmon
sysstat
dstat
netdata
sysdig






http://www.brendangregg.com/linuxperf.html

https://medium.com/netflix-techblog/netflix-at-velocity-2015-linux-performance-tools-51964ddb81cf

https://www.slideshare.net/brendangregg/linux-performance-tools



[post-content post_name=rhsca-quiz] 

[accordion]
[toggle title="What are the commands to install 2 rpms that can be used to collect and display system utilisation metrics?"]
$ yum install dstat
$ yum install sysstat
[/toggle]
[toggle title="What is the command to view realtime cpu metrics?"]
$ dstat -c
[/toggle]
[toggle title="What is the command to tee the dstat output to a file called /tmp/dstat.csv?"]
$ dstat --output /tmp/dstat.csv
[/toggle]
[toggle title="How does sysstat operate to collect daily historical reports?"]
cronjob
[/toggle]
[toggle title="Where are the daily reports stored and what format are they?"]
- /var/log/sa/sa{dd}
- /var/log/sa/sar{dd}
[/toggle]
[toggle title="What format are these files?"]
binary files. 
[/toggle]
[toggle title="What command can you use to query these files?"]
sadf
[/toggle]
[toggle title="what is the command to query all info stored in /var/log/sa/sa28 during 6pm-7pm, that is also in csv form?"]
$ sadf -d -s 18:00:00 -e 19:00:00 /var/log/sa/sa28
# here we have specified a (d)elimited file with (s)tart and (e)nd date. 
[/toggle]
[toggle title="What is sadf a wrapper for?"]
The sar command. you can also pass in sar command related options and switches by using '--', e.g.:
$ $ sadf -d -s 18:00:00 -e 19:00:00 /var/log/sa/sa28 -urd -n DEV
[/toggle]
[/accordion]