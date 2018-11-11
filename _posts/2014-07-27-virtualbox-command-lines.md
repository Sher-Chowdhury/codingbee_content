---
ID: 131
post_title: virtualbox command lines
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/virtualbox/virtualbox-command-lines
published: true
post_date: 2014-07-27 00:00:00
---
virtualbox comes with it's own command line utility:

https://www.virtualbox.org/manual/ch08.html#vboxmanage-list

to make use of this you first have to cd to the vboxmanage.exe which is in the virtualbox program file's folder:


<pre>C:\Program Files\Oracle\VirtualBox</pre>

here are some examples:

<strong>Start a vm</strong>
use the following:

.\VBoxManage.exe startvm {vm's name}

Note: the vm's name is case sensitive. I think.

e.g.:

[powershell]
PS C:\Program Files\Oracle\VirtualBox=> .\VBoxManage.exe startvm Centos7
Waiting for VM &quot;Centos7&quot; to power on...
VM &quot;Centos7&quot; has been successfully started.
[/powershell]

<strong>List running vms</strong>

This is done like this:

[powershell]
PS C:\Program Files\Oracle\VirtualBox=> .\VBoxManage.exe list runningvms
&quot;Centos7&quot; {6cfe3d82-b4ae-46f1-9188-d5e6a603474e}
PS C:\Program Files\Oracle\VirtualBox=>
[/powershell]


<strong>Shutdown a VM</strong>

To shutdown a vm, do:

[powershell]
PS C:\Program Files\Oracle\VirtualBox=> .\VBoxManage.exe controlvm Centos7 savestate
0%...10%...20%...30%...40%...50%...60%...70%...80%...90%...100%
PS C:\Program Files\Oracle\VirtualBox=>
[/powershell]