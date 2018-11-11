---
ID: 56
post_title: What is PowerShell?
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/what-is-powershell
published: true
post_date: 2014-05-11 00:00:00
---
Powershell is the main command line utility for Microsofts Windows Operating Systems. It comes pre-installed with Windows 7 and onwards. As well as Windows Server 2008 and onwards. It is immensely powerfull and is regularly used my system administrators to automate repetitive windows tasks.

To open up the Powershell Console, go to:

start -> all programs -> accessories -> Windows PowerShell

You might find 2 pairs of links, those tagged with x86, and those without. The x86 are actually the 32bit versions and should be avoided. The "PowerShell ISE" is actually a more feature reach version of the standard PowerShell console, and is as an IDE for writing PowerShell scripts.

Tip, you should also do the following: 

<ol>
	<li>right click on the powershell icon</li>

	<li>go to properties,</li>

	<li>select the shortcut tab</li>

	<li>click on the "advanced" button</li>

	<li>enable the "Run as Administrator" checkbox</li>

	<li>Then apply and save changes</li>
</ol>


The latest version of powershell is v4.0. To see what version you have installed, do:


<pre>
PS C:\=> $PSVersionTable

Name                           Value
----                           -----
PSVersion                      4.0
WSManStackVersion              3.0
SerializationVersion           1.1.0.1
CLRVersion                     4.0.30319.18444
BuildVersion                   6.3.9600.16406
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0}
PSRemotingProtocolVersion      2.2
</pre>


If you or using an older version, then you can upgrade to the latest version by installing the latest <a href="https://www.microsoft.com/en-us/download/details.aspx?id=48729">Windows Management Framework</a>.

In this course we will be showing you how to write and run powershell scripts. However, by default PowerShell's script running capability is disabled (aka restricted). You can check what this setting is by running the following command:



<pre>PS C:\Windows\system32=> Get-ExecutionPolicy
Restricted</pre>



To follow this course, you need to change this to unrestricted, like this:

<pre>
PS C:\> Set-ExecutionPolicy -ExecutionPolicy unrestricted
PS C:> Get-ExecutionPolicy
Unrestricted
</pre>


You only need to do this once, and your machine will remember it, even if you restart it.

We'll explain more about <a title="PowerShell - Security Alert" href="http://codingbee.net/tutorials/powershell/powershell-security-alert/">Execution Policies</a> later in the course, but for now just accept this in order to get started.

Another key feature that is disabled by default is PowerShell's ability to send commands to other windows machines and run those commands remotely. This feature is immensely powerful because it allows Windows SysAdmins to remotely manage an army of windows machines from a single place. We'll explain more about <a href="http://codingbee.net/tutorials/powershell/powershell-run-commands-on-remote-machines/">remoting</a> later in the course, but for now, all you need to do is:


<pre>
PS C:\Windows\system32=> enable-psremoting
</pre>

(You may need to say "Y" to several prompts.)

Once again, you only need to do this once, and your machine will remember it, even if you restart it.

PowerShell comes with a handy built-in help guide. This guide is an comprehensive reference manual that tells you everything you need to know about PowerShell. However this guide gets regularly updated and to ensure you have the most up-to-date guide installed, simply run:


<pre>
PS C:\Windows\system32=> Update-Help
</pre>


We'll cover more about how to use this built-in help guide in the next lesson.