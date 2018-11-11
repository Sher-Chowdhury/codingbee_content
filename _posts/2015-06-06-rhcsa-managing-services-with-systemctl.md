---
ID: 410
post_title: 'RHCSA &#8211; Managing Services with systemctl'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-managing-services-with-systemctl
published: true
post_date: 2015-06-06 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the (rpm) command to list what services are installed when installing the 'httpd' package?"]
$ rpm -ql httpd | grep ".service$"
[/toggle]
[toggle title="What is the command to view all the available options and subcommands for systemctl?"]
$ systemctl <span style="letter-spacing:0.1px">-</span>-help
[/toggle]
[toggle title="What is the command to check the status of the httpd service?"]
$ systemctl status httpd.service
[/toggle]
[toggle title="What is the command to start the httpd service?"]
$ systemctl start httpd.service
[/toggle]
[toggle title="What is the command to enable the httpd service?"]
$ systemctl enable httpd.service
[/toggle]
[/accordion]

<hr/>


<h2>What is a service</h2>
A service is one of the dozen types of unit that systemd manages. 

In Linux, a service is basically an application that is continuously running in the background waiting to be used. A common example of a service, is a web server, for example the "httpd" (aka Apache HTTP web server):

A service is represented by a *.service file, so if you want to check what services that this package has installed, you can do:

<pre>

$ rpm -ql httpd | grep ".service$"
/usr/lib/systemd/system/htcacheclean.service
/usr/lib/systemd/system/httpd.service
</pre>

As you can see, this application comes with 2 services, of which httpd.service is the main service. We'll cover more about *.service files later. 

To manage a service, e.g. start/stop a service, we use the "systemctl" command: 

<pre>
$ systemctl --help
systemctl [OPTIONS...] {COMMAND} ...

Query or send control commands to the systemd manager.

  -h --help           Show this help
     --version        Show package version
  -t --type=TYPE      List only units of a particular type
     --state=STATE    List only units with particular LOAD or SUB or ACTIVE state
  -p --property=NAME  Show only properties by this name
  -a --all            Show all loaded units/properties, including dead/empty
                      ones. To list all units installed on the system, use
                      the 'list-unit-files' command instead.
     --reverse        Show reverse dependencies with 'list-dependencies'
  -l --full           Don't ellipsize unit names on output
     --fail           When queueing a new job, fail if conflicting jobs are
                      pending
     --irreversible   When queueing a new job, make sure it cannot be implicitly
                      cancelled
     --ignore-dependencies
                      When queueing a new job, ignore all its dependencies
     --show-types     When showing sockets, explicitly show their type
  -i --ignore-inhibitors
                      When shutting down or sleeping, ignore inhibitors
     --kill-who=WHO   Who to send signal to
  -s --signal=SIGNAL  Which signal to send
  -H --host=[USER@]HOST
                      Show information for remote host
  -P --privileged     Acquire privileges before execution
  -q --quiet          Suppress output
     --no-block       Do not wait until operation finished
     --no-wall        Don't send wall message before halt/power-off/reboot
     --no-reload      When enabling/disabling unit files, don't reload daemon
                      configuration
     --no-legend      Do not print a legend (column headers and hints)
     --no-pager       Do not pipe output into a pager
     --no-ask-password
                      Do not ask for system passwords
     --system         Connect to system manager
     --user           Connect to user service manager
     --global         Enable/disable unit files globally
     --runtime        Enable unit files only temporarily until next reboot
  -f --force          When enabling unit files, override existing symlinks
                      When shutting down, execute action immediately
     --root=PATH      Enable unit files in the specified root directory
  -n --lines=INTEGER  Number of journal entries to show
  -o --output=STRING  Change journal output mode (short, short-monotonic,
                      verbose, export, json, json-pretty, json-sse, cat)
     --plain          Print unit dependencies as a list instead of a tree

Unit Commands:
  list-units                      List loaded units
  list-sockets                    List loaded sockets ordered by address
  <mark>start</mark> [NAME...]                 Start (activate) one or more units
  <mark>stop</mark> [NAME...]                  Stop (deactivate) one or more units
  reload [NAME...]                Reload one or more units
  <mark>restart</mark> [NAME...]               Start or restart one or more units
  try-restart [NAME...]           Restart one or more units if active
  reload-or-restart [NAME...]     Reload one or more units if possible,
                                  otherwise start or restart
  reload-or-try-restart [NAME...] Reload one or more units if possible,
                                  otherwise restart if active
  isolate [NAME]                  Start one unit and stop all others
  kill [NAME...]                  Send signal to processes of a unit
  is-active [NAME...]             Check whether units are active
  is-failed [NAME...]             Check whether units are failed
  <mark>status</mark> [NAME...|PID...]         Show runtime status of one or more units
  show [NAME...|JOB...]           Show properties of one or more
                                  units/jobs or the manager
  set-property [NAME] [ASSIGNMENT...]
                                  Sets one or more properties of a unit
  help [NAME...|PID...]           Show manual for one or more units
  reset-failed [NAME...]          Reset failed state for all, one, or more
                                  units
  list-dependencies [NAME]        Recursively show units which are required
                                  or wanted by this unit or by which this
                                  unit is required or wanted

Unit File Commands:
  list-unit-files                 List installed unit files
  <mark>enable</mark> [NAME...]                Enable one or more unit files
  <mark>disable</mark> [NAME...]               Disable one or more unit files
  reenable [NAME...]              Reenable one or more unit files
  preset [NAME...]                Enable/disable one or more unit files
                                  based on preset configuration
  is-enabled [NAME...]            Check whether unit files are enabled

  mask [NAME...]                  Mask one or more units
  unmask [NAME...]                Unmask one or more units
  link [PATH...]                  Link one or more units files into
                                  the search path
  get-default                     Get the name of the default target
  set-default NAME                Set the default target

Job Commands:
  list-jobs                       List jobs
  cancel [JOB...]                 Cancel all, one, or more jobs

Snapshot Commands:
  snapshot [NAME]                 Create a snapshot
  delete [NAME...]                Remove one or more snapshots

Environment Commands:
  show-environment                Dump environment
  set-environment [NAME=VALUE...] Set one or more environment variables
  unset-environment [NAME...]     Unset one or more environment variables

Manager Lifecycle Commands:
  daemon-reload                   Reload systemd manager configuration
  daemon-reexec                   Reexecute systemd manager

System Commands:
  default                         Enter system default mode
  rescue                          Enter system rescue mode
  emergency                       Enter system emergency mode
  halt                            Shut down and halt the system
  poweroff                        Shut down and power-off the system
  reboot                          Shut down and reboot the system
  kexec                           Shut down and reboot the system with kexec
  exit                            Request user instance exit
  switch-root [ROOT] [INIT]       Change to a different root file system
  suspend                         Suspend the system
  hibernate                       Hibernate the system
  hybrid-sleep                    Hibernate and suspend the system
</pre>

I have highlighted some of systemctl's most commonly used sub-commands above. 

systemctl doesn't just manage service. It can manage all kinds of other things that can have a state (e.g). These things are referred to as "units".

Here's a list of the different types of units that systemctl can manage:


<pre>
$ systemctl --type=help
Available unit types:
service
socket
target
device
mount
automount
snapshot
timer
swap
path
slice
scope

</pre>

Each unit is represented in the form of a file. All unit files are stored in <code>/usr/lib/systemd/system</code>. 

<h2>Starting/Stopping/Enabling Services</h2>

To check the status of a service we do:

<pre>
$ systemctl status httpd
httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; <mark>disabled</mark>)
   Active: <mark>inactive (dead)</mark>

Jun 06 10:03:18 centos7.codingbee.dyndns.org systemd[1]: Starting The Apache HTTP Server...
Jun 06 10:03:18 centos7.codingbee.dyndns.org systemd[1]: Started The Apache HTTP Server.
Jun 06 10:44:49 centos7.codingbee.dyndns.org systemd[1]: Stopping The Apache HTTP Server...
Jun 06 10:44:51 centos7.codingbee.dyndns.org systemd[1]: Stopped The Apache HTTP Server.
Jun 06 10:44:57 centos7.codingbee.dyndns.org systemd[1]: Starting The Apache HTTP Server...
Jun 06 10:44:57 centos7.codingbee.dyndns.org systemd[1]: Started The Apache HTTP Server.
Jun 06 10:44:59 centos7.codingbee.dyndns.org systemd[1]: Stopping The Apache HTTP Server...
Jun 06 10:45:00 centos7.codingbee.dyndns.org systemd[1]: Stopped The Apache HTTP Server.

</pre> 


This shows that the httpd service is not currently running (i.e. inactive) and it wont start up by default when you're booting up the machine (i.e. it is disabled). This command also provides a short log history. 



So to start a service, we do:

<pre>
$ systemctl start httpd
$
</pre>

This doesn't give a success output, so let's check the status again to see if this has been successful:

<pre>
$ systemctl status httpd
httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; <mark>disabled</mark>)
   Active: <mark>active (running)</mark> since Sat 2015-06-06 11:08:52 BST; 47s ago
 Main PID: 4336 (httpd)
   Status: "Total requests: 0; Current requests/sec: 0; Current traffic:   0 B/sec"
   CGroup: /system.slice/httpd.service
           ├─4336 /usr/sbin/httpd -DFOREGROUND
           ├─4337 /usr/sbin/httpd -DFOREGROUND
           ├─4338 /usr/sbin/httpd -DFOREGROUND
           ├─4339 /usr/sbin/httpd -DFOREGROUND
           ├─4340 /usr/sbin/httpd -DFOREGROUND
           └─4341 /usr/sbin/httpd -DFOREGROUND

Jun 06 11:08:52 centos7.codingbee.dyndns.org systemd[1]: Starting The Apache HTTP Server...
Jun 06 11:08:52 centos7.codingbee.dyndns.org systemd[1]: Started The Apache HTTP Server.

</pre>


Here we can see that the service is now running now. However the service is still disabled. This means that this service won't be automatically started when you reboot the machine. You can therefore enable this service:


<pre>
$ systemctl enable httpd
ln -s '/usr/lib/systemd/system/httpd.service' '/etc/systemd/system/multi-user.target.wants/httpd.service'
</pre>

Note: This essentially creates a soft link in the "multi-user.target.wants" folder. We'll cover more about this later.  



Let's now check the status again:

<pre>
$ systemctl status httpd
httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; <mark>enabled</mark>)
   Active: active (running) since Sat 2015-06-06 11:08:52 BST; 5min ago
 Main PID: 4336 (httpd)
   Status: "Total requests: 0; Current requests/sec: 0; Current traffic:   0 B/sec"
   CGroup: /system.slice/httpd.service
           ├─4336 /usr/sbin/httpd -DFOREGROUND
           ├─4337 /usr/sbin/httpd -DFOREGROUND
           ├─4338 /usr/sbin/httpd -DFOREGROUND
           ├─4339 /usr/sbin/httpd -DFOREGROUND
           ├─4340 /usr/sbin/httpd -DFOREGROUND
           └─4341 /usr/sbin/httpd -DFOREGROUND

Jun 06 11:08:52 centos7.codingbee.dyndns.org systemd[1]: Starting The Apache HTTP Server...
Jun 06 11:08:52 centos7.codingbee.dyndns.org systemd[1]: Started The Apache HTTP Server.
</pre>


Now it is enabled. If we now stop the service and then reboot the machine, by default this service will now automatically start up again during the boot process.


<h2>Viewing services</h2>

To view all currently running services, we do:


<pre>
$ systemctl list-units --type=service
UNIT                                                   LOAD   ACTIVE SUB     DESCRIPTION
abrt-ccpp.service                                      loaded active exited  Install ABRT coredump hook
abrt-oops.service                                      loaded active running ABRT kernel log watcher
abrt-xorg.service                                      loaded active running ABRT Xorg log watcher
abrtd.service                                          loaded active running ABRT Automated Bug Reporting Tool
accounts-daemon.service                                loaded active running Accounts Service
alsa-state.service                                     loaded active running Manage Sound Card State (restore and store)
atd.service                                            loaded active running Job spooling tools
|
|
|..etc

</pre>


To view all services irrespective of whether they are running or not, we do:

<pre>
$ systemctl list-unit-files --type=service
UNIT FILE                                   STATE
abrt-ccpp.service                           enabled
abrt-oops.service                           enabled
abrt-pstoreoops.service                     disabled
abrt-vmcore.service                         enabled
abrt-xorg.service                           enabled
abrtd.service                               enabled
accounts-daemon.service                     enabled
alsa-restore.service                        static
alsa-state.service                          static
alsa-store.service                          static
.
.
...etc
</pre>

This is similar to the old chkconfig command, since it shows which services are currently enabled so that they autostart at boot time. However unlike chkconfig, it only shows the info for the current target (which is a bit like a runlevel). 


<h2>Masking services</h2>
If you want to have a service enabled+started but you want to temporarily disable it for troubleshooting purposes, then you can mask the service:


<pre>
$ systemctl mask httpd
Created symlink from /etc/systemd/system/httpd.service to /dev/null.
</pre>

By masking it, it means that you are prevented from running the commands:


<pre>
[root@target ~]# systemctl stop httpd
[root@target ~]# systemctl status httpd
● httpd.service
   Loaded: masked (/dev/null; bad)
   Active: inactive (dead) since Fri 2018-03-02 16:32:25 UTC; 1s ago
 Main PID: 20219 (code=exited, status=0/SUCCESS)
   Status: "Total requests: 0; Current requests/sec: 0; Current traffic:   0 B/sec"
$ systemctl start httpd
Failed to start httpd.service: Unit is masked.
$ systemctl disable httpd
$ systemctl enable httpd
Failed to execute operation: Cannot send after transport endpoint shutdown
</pre>

Notice you can still stop+disable a masked service. But you prevented from taking any action that could let the deamon starts up. 

Also notice that if a service is running at the time of masking then it will continue to run until it is stopped. 


To reverse the masking you do:

<pre>
$ systemctl unmask httpd
</pre>