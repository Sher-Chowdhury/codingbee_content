---
ID: 419
post_title: SELinux Overview
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-selinux-overview
published: true
post_date: 2015-06-18 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to view user guide about selinux?"]
$ man selinux
[/toggle]
[toggle title="What are the 2 main types of security layers, and which type is Selinux?"]
- Discretionary Access Control  
- Mandatory Access Control  (selinux is this type)
[/toggle]
[toggle title="What are the SELinux packages that you should always install before working wiht SElinux?"]
- policycoreutils-python
- policycoreutils-gui
- policycoreutils-devel
- setools-console
- setools-gui
- setroubleshoot
- setroubleshoot-server
[/toggle]
[/accordion]

<hr/>


<h2>What is SELinux?</h2>
SELinux is a system that is primarily used for protecting your machine from potential attacks from the internet.  For example there are a lot of programs, e.g. apache that comes with it's own service-user-account e.g. A user called "apache" is created as part of doing a yum install of the httpd rpm. Hackers may be able to find a way to take control of that service-user-account and consequently gain access to other files and folders on your machine.

In this situation the traditional ugo+rwx permission just doesn't offer a way to combat this kind of vulnerability. And that is where SELinux comes to the rescue. 

SELinux essentially does damage control. That is, if the apache software has been compromised in any way, then the attacker is limited to accessing only files+folders that the apache-user-account would normally access anyway. Therefore an attacker (while masquarading as the apache user) won't be able to read/write anything in the /etc folder even if the apache service account has the necessary ugo+rwx privileges for this folder. 

In later article we'll cover later how SELinux manages to achieve this kind of granular access control. In the meantime the following man page gives a short overview of SELinux:

<pre>
$ man selinux
</pre>

The only situation where you might not need SELinux, is if your machine does not have internet access. 


<h2>Layers of Security</h2>
To make a machine secure, you add "layers" of security. SELinux is essentially just another layer of security. 

In Linux, there are lots of layers of security, e.g. firewall, ugo+rwx, dmz, password policies, encryption, authentication....etc.  However all these layers doesn't harden security at the OS level. But SELinux does, in fact it's support is built into the kernel. That is why it is referred to as a <strong>Mandatory Access Control</strong>, aka MAC. 

All the other security layers, e.g. ugo+rwx and firewalls, are referred to as <strong>Discretionary Access Control</strong> (aka DAC). All DAC layers are vulnerable to human error. For example the root user who can inadvertently open up vulnerabilities. Whereas SELinux cannot be made vulnerable by mistake. 

So how do all these DAC and MAC layers work together? When one objects (e.g. a process) attempts to access another object (e.g. config file), then this requests first needs to be approved by all the DAC security layers, and after that it finally needs to get approved by the mac ( SELinux) layer. Access is permitted once the request is permitted by all the security layers.


<h2>Handy SELinux related utilities</h2>

Here are some relevant SELinux utilities that you should install:

<ul>
	<li><strong>policycoreutils-python</strong> - This package provides the semanage command.</li>
	<li><strong>policycoreutils</strong> - provides lot of important selinux commands, including sestatus, restorecon, setsebool</li>

        <li>policycoreutils-gui</li>
	<li><strong>setools-console</strong> - This provides the sesearch command</li>
	<li>setools-gui</li>
	<li>setroubleshoot</li>
	<li>setroubleshoot-server</li>
        <li>selinux-policy-doc</li>
</ul>



I.e. we do:


<pre>
$ yum install policycoreutils-python policycoreutils-gui setools-console setools-gui setroubleshoot setroubleshoot-server

</pre>

Here's a quick description of each of these:


<pre>
$ yum info policycoreutils-python | grep "Description" -A1
Description : The policycoreutils-python package contains the management tools
            : use to manage an SELinux environment.


$ yum info policycoreutils-gui | grep "^Description" -A1
Description : system-config-selinux is a utility for managing the SELinux
            : environment


$ yum info setools-console | grep "^Description" -A7
Description : SETools is a collection of graphical tools, command-line tools,
            : and libraries designed to facilitate SELinux policy analysis.
            :
            : This package includes the following console tools:
            :
            :   secmds          command line tools: seinfo, sesearch
            :   sediff          semantic policy difference tool


$ yum info setools-gui | grep "^Description" -A7
Description : SETools is a collection of graphical tools, command-line tools,
            : and libraries designed to facilitate SELinux policy analysis.
            :
            : This package includes the following graphical tools:
            :
            :   apol          policy analysis tool
            :   seaudit       audit log analysis tool


$ yum info setroubleshoot | grep "^Description" -A8
Description : setroubleshoot GUI. Application that allows you to view
            : setroubleshoot-server messages.
            : Provides tools to help diagnose SELinux problems. When AVC
            : messages are generated an alert can be generated that will give
            : information about the problem and help track its resolution.
            : Alerts can be configured to user preference. The same tools can be
            : run on existing log files.


$ yum info setroubleshoot-server | grep "^Description" -A8
Description : Provides tools to help diagnose SELinux problems. When AVC
            : messages are generated an alert can be generated that will give
            : information about the problem and help track its resolution.
            : Alerts can be configured to user preference. The same tools can be
            : run on existing log files.

</pre>