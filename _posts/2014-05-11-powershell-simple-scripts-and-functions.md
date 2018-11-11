---
ID: 79
post_title: 'PowerShell &#8211; Simple scripts and functions'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-simple-scripts-and-functions
published: true
post_date: 2014-05-11 00:00:00
---
Chapter 4 - Simple scripts and functions

Here is the standard syntax for writing a function in a script:

function hello 
{
    "hello world"
}
hello	# Here I have called the function so that you can see it in action. 

You can also parameterize functions, like this:

function hello 
{
	param(
			[parameter(mandatory=$true)]
			[string]$name 
		 )
    "hello my name is $name"
}
hello	# Here I have called the function so that you can see it in action. 

Notice that scripts and functions uses the same parametization syntax. 

In Linux it is possible to create your own custom commands, which are usually done by:

    1 using aliases  - this option is best for simple commands such as:     alias lsd='ls -d */'
    2 creating a whole script which defines the new custom command. Then update the $PATH variable (ideally in the .profile config time), 
      so that you can call the command (by the script's name) while in any directory, rather than constantly navigating to the script's folder.
      In this scenario, you have a file for each custom command, and hence you can end up with lots of script files. 
    3 You can have one really big script, where each custom command is contained within a function. Then to be able to run each function 
	  as a command, you simply "dot source" script so that the script loads into the current shell. You can then do the dot-sourcing
	  from within the .profile, so it happens on shell startup. 


In PS, you can also do all of the above. 

To create an alias, you use the set-alias command:

PS C:\> set-alias -name list -Value Get-ChildItem 

Note, to delete an alias, you need to use remove-item:

remove-item alias:{alias's name}


In PS, aliases are only limited to simply giving an alternate name to a single command. You cannot use it to store a pre-configured command. e.g. you cannot do:

set-alias FirstService {Get-Service | Select-Object -First 1}		# This will fail and give an error message.



To do something like this, you need to use approach 2 or 3. Approach 2 would be overkill, because it would end up being a script file containing a single line. So approach 3 is best. However you can also place it as a function in the .profile file. Another option is to define the function straight from the command line, using the function command:

PS C:\> function FirstService {Get-Service | Select-Object -First 1}
PS C:\> FirstService
Status   Name               DisplayName                           
------   ----               -----------                           
Stopped  Alerter            Alerter         


Approach 2 also works in PS, but there is no such thing as a $path variable. One way around this is to link up the script file to an alias:

C:\PS>Set-Alias -name {command's name} -value {path\to\file}

Note: I could be wrong about there not being a "$path" variable in ps, that's because see:
PS C:\> (Get-ChildItem Env:\Path | Select-Object -Property 'value') -split ";"


The final option is to create a script that contains lots of functions, and then dot source it just like linux. E.g. assume we have the following 
functions written in a script called "test.ps1", then do:

PS C:\> get-content .\test.ps1
function hello {"hello world"}
function goodbye {"goodbye world"}

Now dot source it:

PS C:\> . .\test.ps1    	# Note, ".\" means current directory, you can also use the full path.  
PS C:\> hello			# now this function runs like a normal command straight from the command line. 
hello world
PS C:\> goodbye
goodbye world

Note, you can even use tab to auto-complete these commands when writing them in the command line. 

To check that the functions has been added into memory do:

PS C:\> Get-Command | Where-Object -FilterScript {$_.name -match "hello"}


So far we have looked at three ways to create custom commands

	1. By creating aliases  (This doesn't really create custom commands, but give nicknames to existing commands, and also can help with approach 2)
	2. Create a standalone script to represent a custom command. 
	3. Create a function within a script to represent a custom command, then dot source the script

There is actually another (much better) way to create custom commands. And that is by creating custom commands in the form of modules. With modules, you can:

	- Define a way to load a script (and it's functions) into memory
	- Allow the functions to run on demand
	- And easily remove all those functions from memory