---
ID: 409
post_title: 'Systemd &#8211; An overview'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-understanding-systemd
published: true
post_date: 2015-06-05 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command for managing 'units', and how do you view it's help info?"]
$ systemctl <span style='letter-spacing=0.1px'>-</span>-help

[/toggle]
[toggle title="What is the command to view a full list of the types of units there are?"]
$ systemctl <span style='letter-spacing=0.1px'>-</span>-type=help
[/toggle]
[toggle title="What is the command to list every single units, of all types and irrespective whether they are enabled or not?"]
$ systemctl list-unit-files
[/toggle]
[toggle title="What is the command that is the same as above but this time only display service units?"]
$ systemctl list-unit-files <span style='letter-spacing=0.1px'>-</span>-type service
[/toggle]
[toggle title="What is the command that is the same as above but this time only display running units?"]
$ systemctl list-units <span style='letter-spacing=0.1px'>-</span>-type=service
[/toggle]
[toggle title="What is the command to check the status of the sshd.service unit?"]
$ systemctl status sshd.service
[/toggle]
[toggle title="Which directory houses all your unit files?"]
/usr/lib/systemd/system
[/toggle]
[/accordion]

<hr/>



During the boot process, systemd is started right after the kernel has been loaded.  

Systemd is a big part of the RHEL OS. The main command that's used to query/manage systemd is <code>systemctl</code>:

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
  start [NAME...]                 Start (activate) one or more units
  stop [NAME...]                  Stop (deactivate) one or more units
  reload [NAME...]                Reload one or more units
  restart [NAME...]               Start or restart one or more units
  try-restart [NAME...]           Restart one or more units if active
  reload-or-restart [NAME...]     Reload one or more units if possible,
                                  otherwise start or restart
  reload-or-try-restart [NAME...] Reload one or more units if possible,
                                  otherwise restart if active
  isolate [NAME]                  Start one unit and stop all others
  kill [NAME...]                  Send signal to processes of a unit
  is-active [NAME...]             Check whether units are active
  is-failed [NAME...]             Check whether units are failed
  status [NAME...|PID...]         Show runtime status of one or more units
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
  enable [NAME...]                Enable one or more unit files
  disable [NAME...]               Disable one or more unit files
  reenable [NAME...]              Reenable one or more unit files
  preset [NAME...]                Enable/disable one or more unit files
                                  based on preset configuration
  is-enabled [NAME...]            Check whether unit files are enabled

  mask [NAME...]                  Mask one or more units
  unmask [NAME...]                Unmask one or more units
  link [PATH...]                  Link one or more units files into
                                  the search path
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



The main purpose of Systemd is that it essentially controls what "components" to activate/start when you boot up your rhel machine.   

These components are referred to as <em>units</em>, and there are a dozen different types of units: 

<pre>
$ systemctl --type=help
Available unit types:
<mark>service</mark>
socket
<mark>target</mark>
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

The highlighted units are the ones that the RHCSA objectives particularly focuses on.    

If you want to view a list of all units across all unit types, you do:


<pre>
$ systemctl list-unit-files
UNIT FILE                                   STATE
brandbot.path                               disabled
cups.path                                   enabled
systemd-ask-password-console.path           static
systemd-ask-password-plymouth.path          static
abrt-ccpp.service                           enabled
abrt-oops.service                           enabled
abrt-pstoreoops.service                     disabled
abrt-vmcore.service                         enabled
abrt-xorg.service                           enabled
.
....etc
</pre>

Tip: Use tab+tab autocomplete to write systemctl more quickly.

You can then view units by type, e.g. the service units only:


<pre>
$ systemctl list-unit-files --type service
UNIT FILE                                   STATE
abrt-ccpp.service                           enabled
abrt-oops.service                           enabled
abrt-pstoreoops.service                     disabled
abrt-vmcore.service                         enabled
abrt-xorg.service                           enabled
.
...etc
</pre>


Within this list you can filter further to view just the enabled services, like this: 

<pre>
$ systemctl list-units --type service
UNIT                               LOAD   ACTIVE SUB     DESCRIPTION
abrt-ccpp.service                  loaded active exited  Install ABRT coredump hook
abrt-oops.service                  loaded active running ABRT kernel log watcher
abrt-xorg.service                  loaded active running ABRT Xorg log watcher
abrtd.service                      loaded active running ABRT Automated Bug Reporting Tool
accounts-daemon.service            loaded active running Accounts Service

</pre>


You can view the status of particular unit like this:



<pre>
$ systemctl status sshd
sshd.service - OpenSSH server daemon
   Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled)
   Active: active (running) since Sun 2015-09-20 08:15:32 BST; 11h ago
 Main PID: 1012 (sshd)
   CGroup: /system.slice/sshd.service
           └─1012 /usr/sbin/sshd -D

Sep 20 08:15:32 puppetmaster.local systemd[1]: Started OpenSSH server daemon.
Sep 20 08:15:32 puppetmaster.local sshd[1012]: Server listening on 0.0.0.0 port 22.
Sep 20 08:15:32 puppetmaster.local sshd[1012]: Server listening on :: port 22.
Sep 20 08:15:37 puppetmaster.local sshd[1256]: Accepted publickey for vagrant from 10.0.2.2 port ...:13
Sep 20 08:17:59 puppetmaster.local sshd[4878]: Accepted password for root from 192.168.50.1 port ...sh2
Hint: Some lines were ellipsized, use -l to show in full.

</pre>

Each unit comes with it's own config file, which is referred to as "unit files". All the unit files are house in the <code>/usr/lib/systemd/system</code> directory (The above output also provides the full path to corresponding unit file). This directory contains a lot of unit files:





<pre>
$ ls -l
total 1300
-rw-r--r--. 1 root root   275 Jun 10 17:11 abrt-ccpp.service
-rw-r--r--. 1 root root   380 Jun 10 17:11 abrtd.service
-rw-r--r--. 1 root root   361 Jun 10 17:11 abrt-oops.service
-rw-r--r--. 1 root root   266 Jun 10 17:11 abrt-pstoreoops.service
-rw-r--r--. 1 root root   262 Jun 10 17:11 abrt-vmcore.service
-rw-r--r--. 1 root root   311 Jun 10 17:11 abrt-xorg.service
-rw-r--r--. 1 root root   421 Jun 10  2014 accounts-daemon.service
-rw-r--r--. 1 root root   501 Mar  5  2015 alsa-restore.service
-rw-r--r--. 1 root root   558 Mar  5  2015 alsa-state.service
-rw-r--r--. 1 root root   412 Mar  5  2015 alsa-store.service
-rw-r--r--. 1 root root   645 Mar 26 10:43 anaconda-direct.service
-rw-r--r--. 1 root root   185 Mar 26 10:43 anaconda-nm-config.service
-rw-r--r--. 1 root root   660 Mar 26 10:43 anaconda-noshell.service
-rw-r--r--. 1 root root   387 Mar 26 10:43 anaconda.service
-rw-r--r--. 1 root root   684 Mar 26 10:43 anaconda-shell@.service
-rw-r--r--. 1 root root   322 Mar 26 10:43 anaconda-sshd.service
-rw-r--r--. 1 root root   312 Mar 26 10:43 anaconda.target
drwxr-xr-x. 2 root root  4096 Sep 19 21:01 anaconda.target.wants
-rw-r--r--. 1 root root   498 Mar 26 10:43 anaconda-tmux@.service
-rw-r--r--. 1 root root   275 Jun 10  2014 arp-ethers.service
-rw-r--r--. 1 root root   205 Oct  7  2014 atd.service
-rw-r-----. 1 root root   669 Mar  5  2015 auditd.service
-rw-r--r--. 1 root root   663 Mar  6  2015 auth-rpcgss-module.service
-rw-r--r--. 1 root root   346 Mar  5  2015 autofs.service
lrwxrwxrwx. 1 root root    14 Sep 16 19:35 autovt@.service -> getty@.service
-rw-r--r--. 1 root root  1044 Mar  5  2015 avahi-daemon.service
-rw-r--r--. 1 root root   874 Mar  5  2015 avahi-daemon.socket
-rw-r--r--. 1 root root   546 Sep 15 14:21 basic.target
.
.
</pre>

Each unit file's name has a suffix to indicate what type of unit it is. 


Next we'll take a look at what's inside a unit file.