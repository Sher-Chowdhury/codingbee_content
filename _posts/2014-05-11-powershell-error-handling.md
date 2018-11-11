---
ID: 84
post_title: 'PowerShell &#8211; Error Handling'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-error-handling
published: true
post_date: 2014-05-11 00:00:00
---
In PowerShell there are 2 kinds of errors:
<ul>
	<li><strong>Non-terminating error</strong> - This is any error that doesn't bring PowerShell to a standstill. You can think of it as a minor error.</li>
	<li><strong>Terminating error</strong> (aka "exceptions") - This is an error that ps cannot continue from and everything will stop completely.</li>
</ul>
You can control how powershell behaves when it encounters an error. This is done by changing the value of the following reserved variable:

[powershell]
$ErrorActionPreference
Continue
[/powershell]

This variable can take the following 4 values:
<ul>
	<li><strong>Continue</strong> -   This is the default. This setting tells ps to keep going as far as possible, but show any error messages along the way.</li>
	<li><strong>SilentlyContinue</strong> - As "continue" but suppress displaying any error/warning messages, unless it is a terminating error.</li>
	<li><strong>Stop</strong> - This stops everything when it encounters either types of errors. By default it will display the error message. This is a useful setting to use when debugging a script.</li>
	<li><strong>Inquire</strong> - This lets you decide whether or not to continue.</li>
</ul>
Some people will set this setting to "SilentlyContinue" at the top of the script to hide any non-terminating errors. This is actually very bad practice becuase it suppress error messages for the script's script. The best practice is to either use "continue" or "stop".

Powershell lets you set ErrorActionPreference settings for each individual commands using the "-ErrorAction" parameter which is one of the <a title="PowerShell - Common Paramaters" href="http://codingbee.net/tutorials/powershell/powershell-common-paramaters/">common parameters</a>. For example:

remove-item c:\path\to\nonexistant\file

this parameter takes the same 4 values as given above. This is a much better approach if you want to apply "SilentlyContinue" for a command that you know always gives very minor, trivial, non-terminating errors.

There is also another common parameter, which is called "-ErrorVariable". This is something that you can use to store error output of a command into a variable, e.g.:

[powershell]
remove-item c:\path\to\nonexistant\file -ErrorVariable RemoveItemErrorMessage -ErrorAction SilentlyContinue

$RemoveItemErrorMessage
remove-item : Cannot find path 'C:\path\to\nonexistant\file' because it does not exist.
At line:1 char:1
+ remove-item c:\path\to\nonexistant\file -ErrorVariable RemoveItemErrorMessage -E ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : ObjectNotFound: (C:\path\to\nonexistant\file:String) [Remove-Item], ItemNotFoundException
+ FullyQualifiedErrorId : PathNotFound,Microsoft.PowerShell.Commands.RemoveItemCommand
[/powershell]

In the above, I also used ErrorAction:SilentlyContinue to avoid any red writing showing up.

I skipped section 10.4 which is all about the "trap" errorhandling construct, which is something that isn't really used that much any more because it has now been superseded by other constructs, but if want, you can read up about it here: help about_trap.

The most conventional way of doing error handling in powershell is by using the "try...catch...finally" construct. Here is the structure of this construct:

[powershell]
try{ 
    get-wmiobject -class win32_BIOS -computername NotOnline -ErrorVariable wmiObjectErrorMessage -ErrorAction SilentlyContinue &quot;hello world 0&quot; 
} 
catch { 
    &quot;Failure encounter with $computername. The failure message is:&quot; | Out-File -Filepath log.txt -append &quot;wmiObjectErrorMessage&quot; | Out-file -Filepath log.txt -append &quot;hello world 1&quot; 
} 
&quot;hello world 2&quot; 
$wmiObjectErrorMessage 
[/powershell]

The outcome of this will vary depending on what -ErrorAction setting you have chosen:
<ul>
	<li><strong> continue</strong> - This will continue the rest of the try-block, and coninue with rest of the script, which means: - 2 error messages appear on screen. once during try, and once outputting $wmiObjectErrorMessage - Catch block isn't triggered - Both "hello world 0" and "hello world 2" are displayed.</li>
	<li><strong>silentlycontinue</strong> - This gives the same outcome as "continue", except that get-wmiobject doesn't output any error messages.</li>
	<li><strong>stop</strong> - This will stop the try-block midway, and trigger the catch-block. As a result: - Both "hello world 1" and "hello world 2" are displayed. But not "hello world 0" because try-block terminated early. - New entries have been appended to log.txt file - output of $wmiObjectErrorMessage is displayed.</li>
	<li><strong>inquiry</strong> - This opens up a pop-up window, prompting user to click: yes | yes to all | halt command | suspend.</li>
</ul>

In the above example we used "-errorvariable" to pass the error message into the catch construct. However PowerShell automatically passes the "exeptions-object" to the catch-block as "$_", which you can use instead. Note that both of these are actually different (but similar) objects, so they give slightly different outputs. Here are some typical examples of the outputs you can get with $_: 


PS C:\=> $_

Get-WmiObject : The RPC server is unavailable. (Exception from HRESULT: 0x800706BA)
At C:\Scope1.ps1:61 char:18
+ get-wmiobject &lt;&lt;&lt;&lt; -class win32_BIOS -computername NotOnline -ErrorAction stop -ErrorVariable wmiObjectErrorMessage + CategoryInfo : InvalidOperation: (:) [Get-WmiObject], COMException + FullyQualifiedErrorId : GetWMICOMException,Microsoft.PowerShell.Commands.GetWmiObjectCommand PS C:\=> $_.exception
The RPC server is unavailable. (Exception from HRESULT: 0x800706BA)

PS C:\=> $_.exception.message
The RPC server is unavailable. (Exception from HRESULT: 0x800706BA)



Notice that both "$_.exception" and "$_.exception.message" gives the same output. That's because by default, "$_.exception" only outputs the message property.

A try-block can be followed by multiple catch-blocks, each of which can be triggered by a different type of "error object". This is really use way to add logic to your code. For example you if want to try and stop an service, then there could be 3 possible outcomes, one success, and 2 errors. It succeeds, name of service doesn't exist (error1), service already stopped (error2). First let's try to replicate error1:

[powershell]
Try {
    Stop-Service -name &quot;xxxxxx&quot; -ErrorAction Stop
}
Catch [System.exception] {
    &quot;Caught by generic exception&quot;

    $_.exception.gettype().fullname
}
[/powershell]

Note: also check out the "<a href="http://technet.microsoft.com/en-us/library/hh847766.aspx">throw</a>" command. 

This outputs:
[powershell]
Caught by generic exception
Microsoft.PowerShell.Commands.ServiceCommandException
[/powershell]

"System.exception" is a general catch-all exception that can catch all exceptiosn. 

"$_.exception.gettype().fullname" This is a way to identify the error type when you try to stop an service that doesn't exist. With this info now known, we can the generic "System.exception" with the more drilled down option:

[powershell]
Try {
    Stop-Service -name &quot;xxxxxx&quot; -ErrorAction Stop
}
Catch [Microsoft.PowerShell.Commands.ServiceCommandException] {
    &quot;This service doesn't exist.&quot;
}
[/powershell]




You can also add a "finally" block at the end. The finally-block is optional, and it's content will run regardless of whether or not an error occured within the try construct.

For more info, check out:

help about_try_catch_finally