---
ID: 373
post_title: 'Processes &#8211; Finding/Killing processes on CentOS/RHEL 7'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-signals-and-stopping-processes
published: true
post_date: 2018-03-29 00:00:00
---
One thing you may want to do is to find and stop a process. There are a few ways to do this, but they all involve sending a process a "signal". A signal is an instruction that can be sent to a process. Processes aren't allowed to ignore an incoming signal.


There's a standard set of signals you can send to a process. The kill command let's you view the full list of available signals:


<pre>
$ kill -l
 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL       5) SIGTRAP
 6) SIGABRT      7) SIGBUS       8) SIGFPE       <strong>9) SIGKILL</strong>     10) SIGUSR1
11) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     <strong>15) SIGTERM</strong>
16) SIGSTKFLT   17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP
21) SIGTTIN     22) SIGTTOU     23) SIGURG      24) SIGXCPU     25) SIGXFSZ
26) SIGVTALRM   27) SIGPROF     28) SIGWINCH    29) SIGIO       30) SIGPWR
31) SIGSYS      34) SIGRTMIN    35) SIGRTMIN+1  36) SIGRTMIN+2  37) SIGRTMIN+3
38) SIGRTMIN+4  39) SIGRTMIN+5  40) SIGRTMIN+6  41) SIGRTMIN+7  42) SIGRTMIN+8
43) SIGRTMIN+9  44) SIGRTMIN+10 45) SIGRTMIN+11 46) SIGRTMIN+12 47) SIGRTMIN+13
48) SIGRTMIN+14 49) SIGRTMIN+15 50) SIGRTMAX-14 51) SIGRTMAX-13 52) SIGRTMAX-12
53) SIGRTMAX-11 54) SIGRTMAX-10 55) SIGRTMAX-9  56) SIGRTMAX-8  57) SIGRTMAX-7
58) SIGRTMAX-6  59) SIGRTMAX-5  60) SIGRTMAX-4  61) SIGRTMAX-3  62) SIGRTMAX-2
63) SIGRTMAX-1  64) SIGRTMAX

</pre>

Signals 9 and 15 are the 2 signals most commonly used. All these signals are documented in section 7 of the  signal's man page:

<pre>
$ man 7 signal
</pre>

Here's an extract of the relevant part:


<pre>
   Standard signals
       Linux supports the standard signals listed below.  Several signal numbers are architecture-dependent, as indicated in the
       "Value" column.  (Where three values are given, the first one is usually valid for alpha and sparc, the  middle  one  for
       x86,  arm, and most other architectures, and the last one for mips.  (Values for parisc are not shown; see the Linux ker-
       nel source for signal numbering on that architecture.)  A - denotes that a signal is absent on the  corresponding  archi-
       tecture.)

       First the signals described in the original POSIX.1-1990 standard.


       Signal     Value     Action   Comment
       ----------------------------------------------------------------------------------------------------
       SIGHUP        1       Term    Hangup detected on controlling terminal
                                     or death of controlling process
       SIGINT        2       Term    Interrupt from keyboard
       SIGQUIT       3       Core    Quit from keyboard
       SIGILL        4       Core    Illegal Instruction
       SIGABRT       6       Core    Abort signal from abort(3)
       SIGFPE        8       Core    Floating point exception
       <strong>SIGKILL       9       Term    Kill signal</strong>
       SIGSEGV      11       Core    Invalid memory reference
       SIGPIPE      13       Term    Broken pipe: write to pipe with no
                                     readers
       SIGALRM      14       Term    Timer signal from alarm(2)
       <strong>SIGTERM      15       Term    Termination signal</strong>
       SIGUSR1   30,10,16    Term    User-defined signal 1
       SIGUSR2   31,12,17    Term    User-defined signal 2
       SIGCHLD   20,17,18    Ign     Child stopped or terminated
       SIGCONT   19,18,25    Cont    Continue if stopped
       SIGSTOP   17,19,23    Stop    Stop process
       SIGTSTP   18,20,24    Stop    Stop typed at terminal
       SIGTTIN   21,21,26    Stop    Terminal input for background process
       SIGTTOU   22,22,27    Stop    Terminal output for background process

       The signals SIGKILL and SIGSTOP cannot be caught, blocked, or ignored.



</pre>

The most commonly used singals are 9 and 15. Signal 15 is the standard signal to tell a process to die gracefully. Signal 9 is used to forcefully terminate a process. These two signals are by far the main signals that are used. Signal 1 is also used occasionally and what this does is that it reloads any related config files. 

It is best practice to only use signal 9 if signal 15 doesn't work.  

To issue a signal to a process we use the "kill" command:

<pre>
$ kill {pid}
</pre> 

The kill command by default will always send signal 15 (i.e. graceful termination). If you want to send signal 9 (immediate forceful termination) instead, the you do it like this:


<pre>
$ kill -s 9 {pid}
</pre>



If you want kill a all processes of a certain name, then you can do this using the killall command:


<pre>
$ killall {process-name}

</pre>

Here's an example:


<pre>
$ vim &
[1] 4747
$ vim &
[2] 4748

[1]+  Stopped                 vim
$ vim &
[3] 4749

[2]+  Stopped                 vim
$ vim &
[4] 4750

[3]+  Stopped                 vim
$ jobs
[1]   Stopped                 vim
[2]   Stopped                 vim
[3]-  Stopped                 vim
[4]+  Stopped                 vim
$ killall -9 vim
[1]   Killed                  vim
[2]   Killed                  vim
[3]-  Killed                  vim
[4]+  Killed                  vim
$ jobs
$


</pre>


There is another command that is similar to the killall command and that is the pkill command:

<pre>
$ pkill {search-term}        
</pre>


This works in a similar way to the killall command. The main difference is that you can kill without knowing the full and proper name of the process.

Be careful when using the pkill command because you might end up killing a process that you need. Hence it is best to first run the  <a href="http://codingbee.net/tutorials/rhcsa/rhcsa-processes/">pgrep -l {search-term}</a> command to confirm that you are going to kill the processes that you to kill.


[post-content post_name=rhsca-quiz]

[accordion]
[toggle title="What command will list a full list of available kill signals?"]
$ kill -l
[/toggle]
[toggle title="Where can you find info about each of these signals?"]
$ man 7 signal
[/toggle]
[toggle title="How do you send the -9 kill signal to a process with id '25'?"]
$ kill -s 9 25
[/toggle]
[toggle title="What is the command to kill all processes called 'httpd'?"]
$ killall httpd
[/toggle]
[toggle title="What is the command to (gracefully) kill all processes matching the search term 'http'?"]
$ pkill http
[/toggle]

[/accordion]