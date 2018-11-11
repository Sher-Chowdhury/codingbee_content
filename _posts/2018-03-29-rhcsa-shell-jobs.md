---
ID: 369
post_title: 'Linux Process &#8211; Managing Shell Jobs on CentOS/RHEL 7'
author: sher
post_excerpt: >
  jobs are processes that are triggered by
  you, when you run various commands in
  your current bash/putty terminal.
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-shell-jobs
published: true
post_date: 2018-03-29 00:00:00
---
In Linux, there are a lot of services that starts when your machine boots up. These services in turn launch lots of processes that the service requires. To view a full list of all these services you do:


<pre>
$  ps -ef | wc -l
158
$  ps aux | wc -l       # alternative command. Not used that much anymore. 
158
</pre>

In my case, I piped the output to wc command to do a line count instead. Hence in my case I have 158 processes that are currently running.

However some of the processes in the above list are not started by a service, instead they are triggered by you, when you run various commands in your current bash/putty terminal. These are a special type of processes, and they are referred to as "jobs". These jobs get terminated as soon as you close your bash/putty terminal. To view a list of your jobs, do:


<pre>
$ ps -u
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root      1367  0.0  2.6 193316 26832 tty1     Ss+  May01   0:11 /usr/bin/Xorg :0 -background none -verbose -auth /run/gdm/auth-for-
root      8886  0.0  0.2 116752  2860 pts/0    Ss   07:01   0:01 -bash
root      9820  0.0  0.2 116248  2336 pts/1    Ss+  07:55   0:00 /bin/bash
root     10578  1.0  0.4 153128  4676 pts/0    T    08:38   0:00 vim
root     10579  0.0  0.1 123372  1380 pts/0    R+   08:38   0:00 ps -u
</pre> 


Sometime a job is keeping your shell busy and stopping you from doing anything else, e.g. when you run the "find" command to search the entire filesystem for a file. In these situations, you can do <code>ctrl+z</code> keyboard shortcut to first pause the process:


<pre>
$ find / -name hello.txt
^Z
[2]+  Stopped                 find / -name hello.txt
$
</pre> 


Then use the bg command to start the process again, but this time running it in the background:


<pre>
$ bg
[2]+ find / -name hello.txt &
</pre> 

You can then monitor the job's progress using the jobs command:


<pre>
$ jobs
[2]-  Done                    find / -name hello.txt
</pre>


There's a shorthand way to make a command run in the background as soon as it is triggered, and that is by ending the command with an ampersand:



<pre>
$ jobs
$ sleep 200 <strong>&</strong>
[1] 3837
$ jobs
[1]+  Running                 sleep 200 &
$
</pre>

Here's another example:


<pre>
$ sleep 600
^Z
[1]+  Stopped                 sleep 600
$ jobs
[1]+  Stopped                 sleep 600
$ bg
[1]+ sleep 600 &
$ jobs
[1]+  Running                 sleep 600 &
</pre>


Note: bg by default always gets applied to the last shell job.


If you want to bring a background job back to the foreground, the you use the fg command followed by the job id:


<pre>
$ sleep 200          # create a process
^Z                                           # send the job the background. 
[1]+  Stopped                 sleep 200
$ sleep 600
^Z
[2]+  Stopped                 sleep 600
$ sleep 300
^Z
[3]+  Stopped                 sleep 300
$ jobs
[1]   Stopped                 sleep 200
[2]-  Stopped                 sleep 600
[3]+  Stopped                 sleep 300
$ bg
[3]+ sleep 300 &
$ jobs
[1]-  Stopped                 sleep 200
[2]+  Stopped                 sleep 600
[3]   Running                 sleep 300 &
$ jobs 2                 # this displays a particular job by it's id. 
[2]+  Stopped                 sleep 600
$ jobs
[1]-  Stopped                 sleep 200
[2]+  Stopped                 sleep 600
[3]   Running                 sleep 300 &
$ bg 2
[2]+ sleep 600 &
$ jobs
[1]+  Stopped                 sleep 200
[2]   Running                 sleep 600 &
[3]-  Running                 sleep 300 &
$ bg 1
[1]+ sleep 200 &
$ jobs
[1]   Running                 sleep 200 &
[2]-  Running                 sleep 600 &
[3]+  Running                 sleep 300 &
$ fg 2                                     # This brings job with id "2" to the foreground
sleep 600
^C                                         # Here we did ctrl+c to terminate the job
[root@localhost ~]# jobs
[1]-  Running                 sleep 200 &
[2]+  Running                 sleep 300 &


</pre>

You can also kill a background job like this:

<pre>
$ kill -9 %{job id}
</pre>

Here we used the "%" to indicate that this is a job id rather than a process id. 

Tip: while using vim, You can use ctrl+z to put vim in the background so that you can look up something. Then use fg to return back to your vim session. 


[post-content post_name=rhsca-quiz] 

[accordion]
[toggle title="What keyboard shortcut can you use to send a running command to the background?"]
ctrl+z
[/toggle]
[toggle title="What commnd lets you view all the commands that are paused/running in the background?"]
$ jobs
[/toggle]
[toggle title="What command can resume a job number 5, but with it still running in the background?"]
$ bg %5
[/toggle]
[toggle title="How can you run a command in the background as soon as it is triggered?"]
At the end of your command type "&" and then press enter. 
[/toggle]
[toggle title="What is the command to bring the job with id '3', to the forefront?"]
$ fg 3
[/toggle]
[toggle title="What is the command to forcefully kill the background job that has the id '2'?"]
kill -9 %2
[/toggle]
[/accordion]