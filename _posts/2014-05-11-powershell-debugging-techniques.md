---
ID: 85
post_title: 'PowerShell &#8211; Debugging Techniques'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-debugging-techniques
published: true
post_date: 2014-05-11 00:00:00
---
Chapter 11 - Debugging Techniques

 Nothing new in this chapter, except for how to use a csv object, lets say you have a file called "testcsvfile.csv":
 
PS C:\> Get-Content .\testcsvfile.csv
title1,title2,title3
a,b,c
d,e,f
g,h,i 

Notice that in this csv file, the first row is a bit like a header row. You can treat the header as properties once you have created a property:

$csvobject = Import-Csv .\testcsvfile.csv

PS C:\> $csvobject

title1                 title2                title3                                                  
------                 ------                ------                                                  
a                      b                     c                                                       
d                      e                     f                                                       
g                      h                     i                                                       

Now you can treat this object as any other object:

PS C:\> $csvobject | gm		# tells you the availabe properties which in this case are title1, title2, and title3.

PS C:\> $csvobject | select-object -property title2
title2
------
b
e
h     
 
PS C:\> $csvobject[1]
title1                title2                         title3                                                  
------                ------                         ------                                                  
d                     e                              f 

PS C:\> $csvobject[1].title2
e




One really useful tool for debugging is the ability to pause/start the script midway through the running of it. In linux you can do this using the 
"read" command (although in linux, the main purpose of read is to accept user inputs). However in PS, you do can do this using the "write-debug" command (which also lets you echo something at the same time as pausing). In the script, simply write something like:

write-debug "the variable `$computername should now equal localhost. The actual value is: $computername" -debug

Note: You have to use the -debug switch to actaully get this working. 

As soon as this command is run, a popup window appears prompting user to choose one of the following options: Yes|Yes to All|Halt Command|Suspend. 
The "halt" option will actually terminate the command midway.  

There is a really cool feature that you can do here using the "suspend" option. if you can click this you will notices the your command prompt changes to ">>>". This means that you are in debug mode, and it is a bit like being inside the script/function's scope. This means you can do things like call a variable to see if it's value is what you expected. You can even change the value!!!  

After you have finished, viewing/changing variables, you can then type exit to leave the debug mode, and return to the popup window. Here you can resume the script, or terminate (halt) it. 

So far we have come acros a few write-* commands for error reporting. Each of these are designed to be used in different scenarios:

write-warning	# Displays warning message in yellow. This is mainly for the benefit of end-users when they report issues to developers. 
write-verbose	# Developers use this to monitor progress of scripts. This is well suited for doing linux equivalent of "echo line256"
write-debug		# Developers use this to pause/start scripts to monitor it's progressing and find bugs. It basically can do a better job than the 
				# "write-verbose" command. 
write-error		# Not sure when this should be used. 



The write-debug commands is a good way to pause a script once each it reaches a certain line in the code. However there is a more sophisticated tool that lets you pause a script in the following ways:

	- When you reach a certain line number (which is the same thing as inserting a "write-debug" into the script).
	- Straight after a particular command has been run
	- stopping as soon as a particular variable has been defined
	- stopping as soon as a particular defined variable is being used. 
	- stopping as soon as a particular variable is being either defined or outputted. 

The tool that can do this is called "set-psbreakpoint" command. To use this command, you have to specify the full path of the script that you want to attach the pause (aka breakpoint) to. As soon as that breakpoint has been reached, you automatically go into the debug mode (>>>). To continue
with the rest of the script, simply do "exit".

Here is an example on how to pause the script when it reaches line 12:

PS C:\> Set-PSBreakpoint -Script C:\path\to\script.ps1 -line 12		# Note, this won't execute line 12, but only lines 1-11.

Here is an example on how to pause the script just before it runs a custom function called "machines":

PS C:\> Set-PSBreakpoint -Script C:\path\to\script.ps1 -command machines

If you want to pause the script just before it defines (writes) a particular variable then do:

PS C:\> Set-PSBreakpoint -Script C:\Scope2.ps1 -Variable var2 -Mode write

If you want to pause the script just before each time it uses (reads) a particular variable then do:

PS C:\> Set-PSBreakpoint -Script C:\Scope2.ps1 -Variable var1 -Mode read

If you want to pasu the script just before it defines a particular variable, or each time it uses it, then do:

PS C:\> Set-PSBreakpoint -Script C:\Scope2.ps1 -Variable var1 -Mode readwrite

To view a list of all the psbreakpoints that you have set up, then do:

Get-PSBreakpoint

To remove/delete a breakpoint, then do:

remove-psbreakpoint -id {id number}		# Alternatively you can just close your ps terminal if you want to delete all PSBreakpoints. 


you can also enable/disable breakpoints like this:

Enable-PSBreakpoint -Id {id number}
Disable-PSBreakpoint -Id {id number}