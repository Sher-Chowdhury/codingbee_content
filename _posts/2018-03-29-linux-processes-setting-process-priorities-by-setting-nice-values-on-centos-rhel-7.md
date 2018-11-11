---
ID: 374
post_title: 'Linux Processes &#8211; Setting process priorities by setting nice values on CentOS/RHEL 7'
author: sher
post_excerpt: >
  Changing a process’s priority is a
  good way to make your machine run more
  efficiently. Changing process priorities
  is to do with increasing/decreasing how
  much CPU time a process can have while
  the machine is running. For example if
  you are setting up a machine to
  primarily run as a web server, then it
  is a good idea to elevate the web-server
  related processes so that they get more
  access to the CPU.
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/linux-processes-setting-process-priorities-by-setting-nice-values-on-centos-rhel-7
published: true
post_date: 2018-03-29 00:00:00
---
Changing a process’s priority is a good way to make your machine run more efficiently. Changing process priorities is to do with increasing/decreasing how much CPU time a process can have while the machine is running. For example if you are setting up a machine to primarily run as a web server, then it is a good idea to elevate the web-server related processes so that they get more access to the CPU.

modifying the priority is to do with changing a process’s “nice” settings. The nice setting takes a value from -20 to +19, where -20 is the highest priority…and…+19 is the lowest priority. Hence the higher the nice value, the more "nice" a process is in allowing other processes to spend more time with the cpu.

You can view the nice values using to the <code>top</code> command, where the nice values are given under the "NI" column:
<pre>$ top - 16:17:48 up 13 min,  2 users,  load average: 0.00, 0.02, 0.05
Tasks: 128 total,   2 running, 126 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.0 sy,  0.0 ni,100.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  1017480 total,   427188 free,   269520 used,   320772 buff/cache
KiB Swap:  2097148 total,  2097148 free,        0 used.   589188 avail Mem

  PID USER      PR  <mark>NI</mark>    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1759 root      20   0  141620   5448   4212 S  0.3  0.5   0:00.41 sshd
    1 root      20   0   59592   7032   3928 S  0.0  0.7   0:01.82 systemd
    2 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kthreadd
    3 root      20   0       0      0      0 S  0.0  0.0   0:00.03 ksoftirqd/0
    5 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kworker/0:0H
    6 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kworker/u2:0
    7 root      rt   0       0      0      0 S  0.0  0.0   0:00.00 migration/0
.
.
.
...etc
</pre>
You can also use the ps command to view nice values:
<pre>$ ps -eo user,pid,nice,pcpu,pmem,vsize,rss,tname,stat,start_time,bsdtime,command | head
USER       PID  NI %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1   0  0.0  0.6  59592  7036 ?        Ss   16:04   0:01 /usr/lib/systemd/systemd --switched-root --system --deserialize 24
root         2   0  0.0  0.0      0     0 ?        S    16:04   0:00 [kthreadd]
root         3   0  0.0  0.0      0     0 ?        S    16:04   0:00 [ksoftirqd/0]
root         5 -20  0.0  0.0      0     0 ?        S&lt;   16:04   0:00 [kworker/0:0H]
root         6   0  0.0  0.0      0     0 ?        S    16:04   0:00 [kworker/u2:0]
root         7   -  0.0  0.0      0     0 ?        S    16:04   0:00 [migration/0]
root         8   0  0.0  0.0      0     0 ?        S    16:04   0:00 [rcu_bh]
root         9   0  0.0  0.0      0     0 ?        S    16:04   0:00 [rcuob/0]
root        10   0  0.0  0.0      0     0 ?        R    16:04   0:00 [rcu_sched]
</pre>
A process's nice value is usually set to "0", (i.e. average priority) as indicated by the NI column above. The PR column is short for "priority", and it is equal to "20+(nice value). This nice range is specified in the <code>nice</code> command's help info:
<pre>$  nice --help
Usage: nice [OPTION] [COMMAND [ARG]...]
Run COMMAND with an adjusted niceness, which affects process scheduling.
With no COMMAND, print the current niceness.  Niceness values range from
-20 (most favorable to the process) to 19 (least favorable to the process).

Mandatory arguments to long options are mandatory for short options too.
  -n, --adjustment=N   add integer N to the niceness (default 10)
      --help     display this help and exit
      --version  output version information and exit

NOTE: your shell may have its own version of nice, which usually supersedes
the version described here.  Please refer to your shell's documentation
for details about the options it supports.

GNU coreutils online help: &lt;http://www.gnu.org/software/coreutils/&gt;
For complete documentation, run: info coreutils 'nice invocation'

</pre>
By default, all processes starts with a nice value of “0”, in other words average priority.

You can specify a nice value at the time of starting a new process:
<pre>
$ nice -n -10 {command}   
</pre>

For example:

<pre>
$ nice -n -10 service vsftpd start    # this starts the ftp server with a higher priority.
</pre>

However if you want to change the nice value of a process that is already running, then you need to use the <code>renice</code> command instead. Here's the renice command's help info:
<pre>$ renice --help

Usage:
 renice [-n]  [-p|--pid] ...
 renice [-n]   -g|--pgrp ...
 renice [-n]   -u|--user ...

Options:
 -g, --pgrp         interpret argument as process group ID
 -n, --priority    specify the nice increment value
 -p, --pid          interpret argument as process ID (default)
 -u, --user &lt;name|id&gt;   interpret argument as username or user ID
 -h, --help             display help text and exit
 -V, --version          display version information and exit

For more information see renice(1).

</pre>
However if you want to change the priority of a command/process that is already running, then you do this like this:
<pre>$ renice -n 5 {process’s-pid-value}
</pre>
Therefore you first need to use ps, to identify the pid of the process you want to reprioritize. Then to check if the renice command has worked, you can use the ps command to see the new nice value:
<pre>$ ps -p  {process’s-pid-value} -o user,pid,nice,tname,start_time,command
</pre>
You can also change the nice value interactively using the top command:
<pre>$ top -p {pid1},{pid2},{pid3},...etc  
</pre>
This will only list the process with the given pid values, which makes it easier to work with, when you are interested in some particular processes. .

While in "top" mode, if you then select “h”, you will find that you need to use the “r” option to renice a process:
<pre>Help for Interactive Commands - procps-ng version 3.3.10
Window 1:Def: Cumulative mode Off.  System: Delay 3.0 secs; Secure mode Off.

  Z,B,E,e   Global: 'Z' colors; 'B' bold; 'E'/'e' summary/task memory scale
  l,t,m     Toggle Summary: 'l' load avg; 't' task/cpu stats; 'm' memory info
  0,1,2,3,I Toggle: '0' zeros; '1/2/3' cpus or numa node views; 'I' Irix mode
  f,F,X     Fields: 'f'/'F' add/remove/order/sort; 'X' increase fixed-width

  L,&amp;,&lt;,&gt; . Locate: 'L'/'&amp;' find/again; Move sort column: '&lt;'/'&gt;' left/right
  R,H,V,J . Toggle: 'R' Sort; 'H' Threads; 'V' Forest view; 'J' Num justify
  c,i,S,j . Toggle: 'c' Cmd name/line; 'i' Idle; 'S' Time; 'j' Str justify
  x,y     . Toggle highlights: 'x' sort field; 'y' running tasks
  z,b     . Toggle: 'z' color/mono; 'b' bold/reverse (only if 'x' or 'y')
  u,U,o,O . Filter by: 'u'/'U' effective/any user; 'o'/'O' other criteria
  n,#,^O  . Set: 'n'/'#' max tasks displayed; Show: Ctrl+'O' other filter(s)
  C,...   . Toggle scroll coordinates msg for: up,down,left,right,home,end

<strong>  k,r       Manipulate tasks: 'k' kill; 'r' renice</strong>  
  d or s    Set update interval
  W,Y       Write configuration file 'W'; Inspect other output 'Y'
  q         Quit
          ( commands shown with '.' require a visible task display window )
Press 'h' or '?' for help with Windows,
Type 'q' or  to continue


</pre>
After that, "renice it", and the nice column should reflect the new nice value straight away.



[post-content post_name=rhsca-quiz]

[accordion]
[toggle title="What is the command to view help info about highest/lowest priority settings?"]
$ nice <span style='letter-spacing:0.1px'>-</span>-help
[/toggle]
[toggle title="What is the command to view all running process's priorities?"]
$ ps -eo pid,nice,command
[/toggle]
[toggle title="What is the command to start vim with priority of -5?"]
$ nice -n -5 vim
[/toggle]
[toggle title="What is the command to change the priority to -10 for the process with the id 2351?"]
$ renice -n -10 2351
[/toggle]
[/accordion]