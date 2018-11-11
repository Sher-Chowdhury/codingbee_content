---
ID: 66
post_title: 'PowerShell &#8211; Run commands on remote machines'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-run-commands-on-remote-machines
published: true
post_date: 2014-05-11 00:00:00
---
PowerShell lets you run commands on a remote machine. You can even run commands that exist on the remote machine, but doesn't exist in your local machine (since you have not installed it on your local machine).

PowerShell manages to do this by using it's own communication protocol called "Web Services for MANagement", aka WS-MAN. WS_MAN is Microsoft's equivalent of Linux's SSH. It operates over http/https, so is easy to route through firewalls, but has a different default port number.

ws-man comes in the form of a background service called "Windows Remote Management" (winrm):


<pre>
PS C:\> get-service winrm
</pre>


When you run a remote command, the output has to travel over a network, and therefore the command output (object) is serialised int xml to make this possible, and then deserialized on the local machine.

There are lots of help pages about remoting:

<pre>
PS C:\> help about_remote*
</pre>


In order for remoting to work:

<ul>
	<li>both machines must have an instance of PSv2 (or above) running, and both instance are for the same user. If this is not the case, then</li>
	<li>Ideally both machines to be on the same domain, or trusting domains (more trickier if seperate domains)</li>
</ul>

WinRM is not a powershell thing only, it is a more general tool that is used by the OS to route traffic to and from other application between machines. Therefore any incoming traffic is tagged with the same name of the recieving application. These applications are  registered with WinRM as endpoints. E.g. for an analogy, WinRM is the royal mail, and each newly built house's address has to be registered with the royal mail, in order to send and recieve letters. Hence powershell also has to register itself with WinRM.

WinRM can have lots of endpoints, and PS calls these endpoints "session configurations". An application can have a number of endpoints, each with
different permissions/functionality.

So before you can start remoting, you first have to set up a listener for your ps, this can be done by running the following command:

&nbsp;

<pre>
enable-psremoting
</pre>


By default, winrm uses ports 5985 (for http) and 5986 (for https)

Note: You can run winrm as a standalone command in ps (just like regedit, and cmd):
winrm # just run this on its own.

note: "enable-psremoting" on it's own might now work, you might also need to update <a href="http://technico.qnownow.com/the-winrm-client-cannot-process-the-request-if-the-authentication-scheme-is-different/">gpedit.msc</a>.

Once you have run the enable-psremoting command, you should now be ready to do a remote connection, which is done like this:


<pre>
enter-pssession -computername {machine name}
</pre>


after that you can run commands remotely and after you are finished, you can exit the remote machine by closing your ps session, or running:


<pre>
exit-pssession
</pre>



If you want to run some commands on multiple machines at the same time, then use:

<pre>invoke-command -computername name1,name2,name3 -scriptblock {command-you-want-to-run}</pre>

Let's first give a quick intro to invoke-command. This command is actually a pretty generic command which basically means
"run the following command". As a result, the first positional+mandatory command that it accepts is "-scriptblock". Therefore
if you want to run the get-service and get-process command (on the local machine) at the same time, then do:

<pre>invoke-command -scriptblock {get-service; get-process}</pre>

However what makes invoke-command really valuable is that it has the "-computername" option. This lets you run the -scriptblock
simultaneously across several machines (as shown further above).

The invoke-command also adds an extra "pscomputername" property to the output, so to keep track of
which machine each object came from

Some commands (e.g. get-eventlog) have native remote functionality builtin. This is indicated by the fact that the command has a parameter called "-computername". In general it is always better, faster, and more reliable to use invoke-command/enter-pssession rather than the native command remoting functions.

If you have lots of commands you want run on several remote machines, then it can become tedious constantly declaring them in the command line. Instead a better approach is to create reusable persistant connection (session) instead. This persistant session can be created using:

<pre>New-PSessionOption</pre>

This command will create the persistant session which is in the form of a "PSSessionOption" object. This object can then be fed into the
-SessionOption parameter in order to establish the connection.

Note: you cannot do a remote connection using a machine's IP address, you have to use the machine's (NetBIOS) name:

https://en.wikipedia.org/wiki/NetBIOS

you can view your machine's netbios name like this:

control panel | system management | general tab

Or alternatively, the name field in:

Get-WmiObject Win32_ComputerSystem # this command is covered later.

If you don't know what you machines name is then you can find it using the machines ip number. However if you don't
know your machine's ip number is, then do.

<pre>ipconfig</pre>

Then to find the name, do:

<pre>nbtstat -a {ip-number}</pre>



Earlier we ran commands remotely using "enter-pssession" and "invoke-command". However the problem with using them is that each time you use these commands, you may have to enter additional info such as passwords and port numbers. This can become repetitive and tedious.

One way around this issue is to create a reusable persistant session:

<pre>New-PSSession -computername manchine1 </pre>

The above can also specify an alternative username, port number...and etc.

The above will create a new (session) object. You can view all session objects like this:

<pre>get-PSSession</pre>

It is handy to store sessions in a variable:

<pre>$server = New-PSSession -ComputerName machine1,machine2,machine3</pre>

The above command creates 3 objects, one for each machine. As a result, $server is actually an array.

Now you can use a session like this:

<pre>enter-pssession -session $server[1] </pre>

another way to write this if you forget the list item and don't want to use gm to check:


<pre>
enter-pssession -session ($server | Where-Object -FilterScript {$_.computername -eq "localhost"}) 
</pre>

Everything in the bracket gets evaluated first. Here is another way:

<pre>get-pssession -computername localhost | enter-pssession</pre>

Notice that we are using "-session" option instead of "-computername". If you check the syntax section with the enter-pssession help pages, you will find that we are using enter-pssession in a different mode.
You can exit a session like this:

<pre>Exit-PSSession # or just do:
Exit</pre>

However the reusable persistant session itself will not be deleted. You have to use the remove-session command to do that:

<pre>remove-PSSession -id {id number}</pre>

Creating an array for storing sessions is useful for 2 purposes:

<ul>
	<li>It lets you create multiple sessions in a single command, rather than running the new-pssession command multiple times.</li>
	<li>It can be used with the invoke command to perform simultaneous task across multiple remote machines.</li>
</ul>

Here is how to use the invoke command using an arrayed session variable:

<pre>
Invoke-Command -ScriptBlock {Get-Service;get-process} -Session $server
</pre>

Notice that this time we used the invoke-command's "-session" option instead of the "-computername" option. A really cool think you can do is kind of create your alias commands, which only run on the remote machine.

E.g. say you want to run get-service and get-process on server[1], then you do:

<pre>
Invoke-Command -ScriptBlock {Get-Service;get-process} -Session $server[1]
</pre>

Now if you want to run the above command numerous times, then typing the above will become tedious.

So one thing you can do is run the above command once, then run:

<pre>import-pssession -session $server[1] -prefix sher</pre>

After that, if you try to run the following commands on your local machine:

<pre>get-sherService
get-sherProcess</pre>

These commands will work, but will actually run the get-service and get-process commands on the remote machine ($server[1]) and retrieve the output.

These aliases are temporarily and will stop working if you close the terminal, or remove the sessions from get-pssession.

This technique has a big drawback - the output retreived are deserialised, hence harder to manage and manipulate. You can see this for yourself
by comparint the following:

<pre>get-sherService | gm # The typename will indicate that it is deserialised.
get-service | gm # this will have a lot more properties and methods.</pre>

You can also disconnect connections, using disconnect-pssession (PS v3 only). But it is something that you will use that much, for more info see:
http://technet.microsoft.com/en-us/library/hh849747.aspx

If you want to change some of the session settings then go to:

<pre>set-location -path WSMan:\localhost\Shell </pre>

Here you will find the following settings:

- idletimeout # specifies how long shell can be idle before timing out (default: 2000 hours)
- maxconcurrentusers # specifies how many different users can have a session open at once.
- maxshellruntime # maximum amount of time a session can stay open, the default is infinite, although idletimeout can over
- maxshellperuser # Maximum of sessions a single user can have open at once.

There is also another setting here:

set-location -path WSMan:\localhost\Service

- MaxConnections # Total number of session that can be opened at once for entire machine.

Useful links:
http://www.howtogeek.com/117192/how-to-run-powershell-commands-on-remote-computers/