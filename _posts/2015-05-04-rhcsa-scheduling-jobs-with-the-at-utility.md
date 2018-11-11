---
ID: 382
post_title: 'RHCSA &#8211; Scheduling jobs with the &#8220;at&#8221; utility'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-scheduling-jobs-with-the-at-utility
published: true
post_date: 2015-05-04 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to check the 'at' utility's service status?"]
$ systemctl status atd
[/toggle]
[toggle title="What is the command to schedule an 'at' task 5 minutes into the future?"]
$ at now+5min
[/toggle]
[toggle title="How do you exit the 'at' utility's interactive mode?"]
ctrl+d
[/toggle]
[toggle title="What is the command to run 'ls -l', and store output to '/tmp/at.log' at 5 mins from now?"]
$ at now+5min
at> ls -l > /tmp/at.log
at> <EOT>                  # ctrl+d
job 1 at Sun May 10 00:22:00 2015
[/toggle]
[toggle title="After the above 'at' task has been created, which directory is the corresponding encrypted file created in?"]
/var/spool/at/
[/toggle]
[toggle title="What is the command to view all the scheduled 'at' tasks that are in the queue?"]
$ atq
# or 
$ at -l
[/toggle]
[toggle title="What is the command to delete 'at' task number '4'?"]
$ atrm 4
[/toggle]
[toggle title="What is the command to schedule an 'at' job at 11:05am on 25th Jan ?"]
$ at 11:05am jan25

[/toggle]
[toggle title="What is the command to schedule the script, '/tmp/test-script.sh' to run 2 minutes from now?"]
$ at -f /tmp/test-script.sh now+2min 
[/toggle]
[/accordion]

<hr/>


If you want to run a one-off job at some point in the future, then you need to use "at" command. 


“at” is feature that is similar to cron. The difference is that “at” is used for automating a one-off scheduled tasks, whereas cron is used for repeating the same task periodically.

For example, if you have a new kernel update that you need to install, then you might want to run that in the early hours of Sunday morning. In which case, you would use “at”.




The "at" solution relies on the atd service for it to function:


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


The “at” command actually starts it’s own interactive shell, here is an example:


<pre>
$ at now+5min
at> ls -l > /tmp/at.log
at> <EOT>
job 1 at Sun May 10 00:22:00 2015
</pre>

You have to do <code>ctrl+d</code> to exit the shell again, after you have finished writing your commands. 

The above "at" session created a file behind then scenes, for each "at" session. You can find them here:


<pre>
$ ls -l /var/spool/at
total 4
-rwx------. 1 root   root   2810 May 10 00:20 a00002016bf9fd
drwx------. 2 daemon daemon    6 Oct  7  2014 spool
</pre>

Each file has an encrypted like name. This file is a script and it will disappear as soon as the the scheduled task has occured. Here's what this file looks like:


<pre>
$ cat /var/spool/at/a00002016bf9fd
#!/bin/sh
# atrun uid=0 gid=0
# mail root 0
umask 22
XDG_SESSION_ID=425; export XDG_SESSION_ID
HOSTNAME=localhost.localdomain; export HOSTNAME
SELINUX_ROLE_REQUESTED=; export SELINUX_ROLE_REQUESTED
SHELL=/bin/bash; export SHELL
HISTSIZE=1000; export HISTSIZE
SSH_CLIENT=192.168.1.244\ 38439\ 22; export SSH_CLIENT
SELINUX_USE_CURRENT_RANGE=; export SELINUX_USE_CURRENT_RANGE
SSH_TTY=/dev/pts/0; export SSH_TTY
USER=root; export USER
LS_COLORS=rs=0:di=01\;34:ln=01\;36:mh=00:pi=40\;33:so=01\;35:do=01\;35:bd=40\;33\;01:cd=40\;33\;01:or=40\;31\;01:mi=01\;05\;37\;41:su=37\;41:sg=30\;43:ca=30\;41:tw=30\;42:ow=34\;42:st=37\;44:ex=01\;32:\*.tar=01\;31:\*.tgz=01\;31:\*.arc=01\;31:\*.arj=01\;31:\*.taz=01\;31:\*.lha=01\;31:\*.lz4=01\;31:\*.lzh=01\;31:\*.lzma=01\;31:\*.tlz=01\;31:\*.txz=01\;31:\*.tzo=01\;31:\*.t7z=01\;31:\*.zip=01\;31:\*.z=01\;31:\*.Z=01\;31:\*.dz=01\;31:\*.gz=01\;31:\*.lrz=01\;31:\*.lz=01\;31:\*.lzo=01\;31:\*.xz=01\;31:\*.bz2=01\;31:\*.bz=01\;31:\*.tbz=01\;31:\*.tbz2=01\;31:\*.tz=01\;31:\*.deb=01\;31:\*.rpm=01\;31:\*.jar=01\;31:\*.war=01\;31:\*.ear=01\;31:\*.sar=01\;31:\*.rar=01\;31:\*.alz=01\;31:\*.ace=01\;31:\*.zoo=01\;31:\*.cpio=01\;31:\*.7z=01\;31:\*.rz=01\;31:\*.cab=01\;31:\*.jpg=01\;35:\*.jpeg=01\;35:\*.gif=01\;35:\*.bmp=01\;35:\*.pbm=01\;35:\*.pgm=01\;35:\*.ppm=01\;35:\*.tga=01\;35:\*.xbm=01\;35:\*.xpm=01\;35:\*.tif=01\;35:\*.tiff=01\;35:\*.png=01\;35:\*.svg=01\;35:\*.svgz=01\;35:\*.mng=01\;35:\*.pcx=01\;35:\*.mov=01\;35:\*.mpg=01\;35:\*.mpeg=01\;35:\*.m2v=01\;35:\*.mkv=01\;35:\*.webm=01\;35:\*.ogm=01\;35:\*.mp4=01\;35:\*.m4v=01\;35:\*.mp4v=01\;35:\*.vob=01\;35:\*.qt=01\;35:\*.nuv=01\;35:\*.wmv=01\;35:\*.asf=01\;35:\*.rm=01\;35:\*.rmvb=01\;35:\*.flc=01\;35:\*.avi=01\;35:\*.fli=01\;35:\*.flv=01\;35:\*.gl=01\;35:\*.dl=01\;35:\*.xcf=01\;35:\*.xwd=01\;35:\*.yuv=01\;35:\*.cgm=01\;35:\*.emf=01\;35:\*.axv=01\;35:\*.anx=01\;35:\*.ogv=01\;35:\*.ogx=01\;35:\*.aac=01\;36:\*.au=01\;36:\*.flac=01\;36:\*.mid=01\;36:\*.midi=01\;36:\*.mka=01\;36:\*.mp3=01\;36:\*.mpc=01\;36:\*.ogg=01\;36:\*.ra=01\;36:\*.wav=01\;36:\*.axa=01\;36:\*.oga=01\;36:\*.spx=01\;36:\*.xspf=01\;36:; export LS_COLORS
MAIL=/var/spool/mail/root; export MAIL
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin; export PATH
PWD=/root; export PWD
LANG=en_GB.UTF-8; export LANG
SELINUX_LEVEL_REQUESTED=; export SELINUX_LEVEL_REQUESTED
HISTCONTROL=ignoredups; export HISTCONTROL
SHLVL=1; export SHLVL
HOME=/root; export HOME
LOGNAME=root; export LOGNAME
SSH_CONNECTION=192.168.1.244\ 38439\ 192.168.1.124\ 22; export SSH_CONNECTION
LESSOPEN=\|\|/usr/bin/lesspipe.sh\ %s; export LESSOPEN
XDG_RUNTIME_DIR=/run/user/0; export XDG_RUNTIME_DIR
cd /root || {
         echo 'Execution directory inaccessible' >&2
         exit 1
}
${SHELL:-/bin/sh} << 'marcinDELIMITER19cb82f9'
<strong>ls -l > /tmp/at.log                                    # The "at" job's main task. </strong>

marcinDELIMITER19cb82f9
</pre>

As you can see this is a shell script, where the "at" job's main task is close to the bottom. 

To view a list of all jobs in the (q)ueue, you use the atq command:

<pre>
$ atq
3       Sun May 10 00:51:00 2015 a root
4       Sun May 10 01:22:00 2015 a root
5       Sun May 10 01:23:00 2015 a root
</pre>

Or alternatively you can usingh the "at" command's -l option:

<pre>
$ at -l
3       Sun May 10 00:51:00 2015 a root
4       Sun May 10 01:22:00 2015 a root
5       Sun May 10 01:23:00 2015 a root
</pre>
 


The first column's display's the job's id. Which is useful if you want to kill a job, which you do using the atrm command:


<pre>
$ atrm 4
$ atq
3       Sun May 10 00:51:00 2015 a root
5       Sun May 10 01:23:00 2015 a root
</pre>




<h2>Picking a schedule time</h2>

Earlier we picked a time that was 5 minutes from now:


<pre>
$ at now+5min
....
</pre>


Note: the shortest unit you can do is minutes, hence you can’t do seconds.


However any of the following would have also worked:

<pre>
at now+5hour
at now+5day
at now+5week
at now+5month
at now+5year
</pre>


You can also run your command at a more specific time like this:



<pre>$ at 11:05am jan25         </pre>

Or if it's on the current day, you cand do:


<pre>$ at 11:05am    </pre>


Or you can even user more human friendly wordings, e.g.:


<pre>
$ at 14:15 tomorrow
</pre>

<h2>scheduling a shell script</h2>

You can also instruct “at” to run a shell script file, rather than entering the commands using the interactive terminal. This is done using the "f" option, here's is an example of this:


<pre>
$ at -f /home/root/test-script.sh now+2min   
</pre>