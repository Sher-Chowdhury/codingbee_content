---
ID: 372
post_title: 'RHCSA &#8211; Monitoring system load using the Top command'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-monitoring-system-load-using-the-top-command
published: true
post_date: 2015-05-02 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to view real time info for the processes with the pids '20428' and '1'?"]
top -p 20428,1
[/toggle]

[/accordion]

<hr/>





If you want to see how much load your machine is under, then we use the top command:


<pre>
$ top
</pre>

This gives real-time-updating output of:



<pre>
top - 22:07:24 up  2:54,  2 users,  <strong>load average: 0.00, 0.01, 0.05</strong>
Tasks: 128 total,   3 running, 125 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.0 sy,  0.0 ni,100.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  1017480 total,   392620 free,   297988 used,   326872 buff/cache
KiB Swap:  2097148 total,  2097148 free,        0 used.   560664 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
    1 root      20   0   59592   7040   3928 S  0.0  0.7   0:03.67 systemd
    2 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kthreadd
    3 root      20   0       0      0      0 S  0.0  0.0   0:00.17 ksoftirqd/0
    5 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kworker/0:0H
.
.
.
</pre>

On the first line we have a "load average" entry, followed by 3 values. 

These values shows the average number of processes in the queue (waiting to be allocated a cpu) in the last 1, 5, and 15 minutes respectively. If these values are successfully increasing then it means that the machine's load is declining. However if it is successively increasing then it means that the machine load is increasing. 


By default, the top command only lists the top 30 or so processes, order by cpu usage. If you want to use top to monitor certain processes, then you can do:
<pre>
$ top -p {pid1},{pid2},{pid3},...etc  
</pre>

This will only list the process with the given pid values, which makes it easier to work with, when you are interested in some particular processes. .

Top is an interactive utility and While in "top" mode, if you then select “h”, you will find various options available:

<pre>
<span style='font-size:12px'>Help for Interactive Commands - procps-ng version 3.3.10
Window 1:Def: Cumulative mode Off.  System: Delay 3.0 secs; Secure mode Off.

  Z,B,E,e   Global: 'Z' colors; 'B' bold; 'E'/'e' summary/task memory scale
  l,t,m     Toggle Summary: 'l' load avg; 't' task/cpu stats; 'm' memory info
  0,1,2,3,I Toggle: '0' zeros; '1/2/3' cpus or numa node views; 'I' Irix mode
  f,F,X     Fields: 'f'/'F' add/remove/order/sort; 'X' increase fixed-width

  L,&,<,> . Locate: 'L'/'&' find/again; Move sort column: '<'/'>' left/right
  R,H,V,J . Toggle: 'R' Sort; 'H' Threads; 'V' Forest view; 'J' Num justify
  c,i,S,j . Toggle: 'c' Cmd name/line; 'i' Idle; 'S' Time; 'j' Str justify
  x,y     . Toggle highlights: 'x' sort field; 'y' running tasks
  z,b     . Toggle: 'z' color/mono; 'b' bold/reverse (only if 'x' or 'y')
  u,U,o,O . Filter by: 'u'/'U' effective/any user; 'o'/'O' other criteria
  n,#,^O  . Set: 'n'/'#' max tasks displayed; Show: Ctrl+'O' other filter(s)
  C,...   . Toggle scroll coordinates msg for: up,down,left,right,home,end

  k,r       Manipulate tasks: 'k' kill; 'r' renice 
  d or s    Set update interval
  W,Y       Write configuration file 'W'; Inspect other output 'Y'
  q         Quit
          ( commands shown with '.' require a visible task display window )
Press 'h' or '?' for help with Windows,
Type 'q' or <Esc> to continue</span>
</pre>


There is also a tui version of the top command called htop. Which you need to install:

<pre>
$ yum install htop
</pre>

Then do:

<pre>
$ htop
</pre>

See also:
http://www.thegeekstuff.com/2010/01/15-practical-unix-linux-top-command-examples/
http://www.tecmint.com/12-top-command-examples-in-linux/