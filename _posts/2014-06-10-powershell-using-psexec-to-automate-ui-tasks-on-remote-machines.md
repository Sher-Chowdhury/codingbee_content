---
ID: 113
post_title: 'PowerShell &#8211; Using psexec to automate UI tasks on remote machines'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-using-psexec-to-automate-ui-tasks-on-remote-machines
published: true
post_date: 2014-06-10 00:00:00
---
When you use PowerShell native remoting commands, e.g. invoke-command, the remote server acts as a terminal server, and doesn't have an active UI session. This means that you can't do something like this:

[powershell]
powershell -computername {name} -scriptblock {notepad.exe}
[/powershell]

Officially there are no powershell cmdlets that you can use to remotely control the gui.

Instead you have to use another microsoft owned utility called "psexec". This is a standalone exe that you can download for free. However psexec comes as part of a bundle of other exe. This bundle is called <a href="http://technet.microsoft.com/en-gb/sysinternals/bb896649.aspx">pstools</a>.

So all you need to do is
<ol>
	<li>download pstools (which is a zip file),</li>
	<li>unblock it (right click on the zip file | properties | unblock button) if necessary,</li>
	<li>then just extract the psexec.exe</li>
	<li>Place the file in a memorable place, preferably in the one of the following directories: $env:path.split(";")</li>
</ol>
&nbsp;

Now from the cmd or powershell, you can do:

[cmd]
& psexec.exe \\{machine-name}  -u {domain}\{username} -p {username} -w c:\temp -d -h -i 2 notepad.exe
[/cmd]

Note: the ampersand is a way to tell powershell to pass the rest of the line to cmd. However I think you can leave out the ampersand and will still work in Powershell.  

One of the first things you may need to do is:

[powershell]
psexec.exe \\$servername -u os\$username -p $password -w c:\temp -d -h  powershell.exe &quot;Enable-PSRemoting -Force&quot;
[/powershell]

You can run the above from within a powershell terminal and don't even need to start the command with an ampersand. Also the variables are automatically first evaluated before psexec.exe takes over :)

You can find the remote session id here:

[powershell]
qwinsta /SERVER:{server-name}   
[/powershell]

This outputs a table in the form of a really long string, which is tricky to deal with, but you can retrieve the session id, using: 

[powershell]
$DirtyQwinstaArray = (qwinsta /SERVER:{machine-name}| Where-Object -FilterScript {$_ -match &quot;{username}&quot;}) -split (&quot; &quot;)
# &quot;DirtyQwinstaArray&quot; contains array items that are just whitespace, these needs to be cleaned up. 

$CleanQwinstaArray = @()
$DirtyQwinstaArray | ForEach-Object {

    if ($_ -eq $DirtyQwinstaArray[2]){
       # &quot;null found&quot;
    }
    else {
       $CleanQwinstaArray += $_
    }
}

$RDPsessionID = $CleanQwinstaArray[2]
[/powershell]


However if you don't have an active session, then I think you can set one via a powershell script using "cmdkey" and "mstsc":

http://stackoverflow.com/questions/11296819/run-mstsc-exe-with-specified-username-and-password

http://technet.microsoft.com/en-us/library/cc754243.aspx


usefule links:

http://stackoverflow.com/questions/17563505/enable-psremoting-remotely

http://forum.sysinternals.com/psexec-i-does-not-interact-with-remote-desktop_topic14473.html

http://www.reddit.com/r/PowerShell/comments/1khn9o/a_fun_script_for_friday_make_your_friends/

&nbsp;