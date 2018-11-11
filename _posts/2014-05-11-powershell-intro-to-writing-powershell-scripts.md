---
ID: 73
post_title: 'PowerShell &#8211; Intro to writing PowerShell scripts'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-intro-to-writing-powershell-scripts
published: true
post_date: 2014-05-11 00:00:00
---
Chapter 21 - You call this scripting?

In the script panel of PS ISE, you can write a bunch of commands, and then select a secion, right click, and then run that selection. 
 
Here is how you parametise a command:

get-service | out-file -FilePath testfile.txt
$filename = "testfile.txt"
get-content .$filename

Note: "out-file" is the linux equivalent of the ">" piping symbol. If you want to do ">>", then use the append option:
get-service | out-file testfile.txt -append 

An alternative way to do ">>" is by using the add-content command:
write 'testline1' | Out-File -FilePath testfile.txt -Encoding utf8		# Note: you might use to use this encoding option to avoid getting 
																		# japanese/chinese characters appearing.
Add-Content -LiteralPath testfile.txt 'testline2'


In a PS script, you can break a single command line into smaller lines in the following ways:

get-service | 		# By ending a line with a pipe
Select-Object `		# By using back-ticks. This escapes the special end-of-command meaning for the return carriage at the end of the line. (linux uses back-ticks differently)
-First 10

Note: You can use the above technique on the PS command line, this technique can only be used within the powershell script. 


If you want a powershell script to accept parameters (e.g. the environment variable) then you need the "param" construct at the start of the script:

param(
[string] $Environment = 'stress',			# Note each line ends with a comma (except for the last line). 
[string] $Application = 'dropbox',
[switch] $RecommendedSettings = $true		# Note, this doesn't require any quotes since switches cannot accept strings. Also we used "$true" which is a special reserved variable. 
)

In the above example, these are positional paramaters, which have default values if you don't declare them. So if you accepts the defaults 
(by not providing parameters) then you will effectively install dropbox on the stress environment using the recommended settings. 

 
If the script is called "InstallApps.ps1", then you can run the script from the command line in the following ways:

.\InstallApps.ps1 -Environment regression -Application firefox -RecommendedSettings true

.\InstallApps.ps1 -Env regression -App firefox -Recom true		# Here we shortened parameter names

.\InstallApps.ps1 regression firefox true				# Here we are using the idea of positional parameters. 

.\InstallApps.ps1		# Here we are using the default parameter values. Hence will end up installing Dropbox on Stress, using recommended settings.

.\InstallApps.ps1	-app firefox 	# here we are accepting the defaults axcept that we are installing firefox instead of dropbox.  

Note, you have first cd to the correct directory before running the above commands. Alternatively you can specify the full path, e.g.:

C:\Documents and Settings\SChowdhury\Desktop\InstallApps.ps1 -Env regression -App firefox -Recom true

You can add comments in your script like this:

# This is a comment on a single line

<#
This is a 
block of comments. 
#>

You can also create your own custom help pages for your script. This is embeded in your script and has a special syntax. It is usually placed at
the very top of the script, even before the parameter declaration section. (see page 262 for an example of this syntax)

 
You can then view the scripts help like this:

help .\InstallApps.ps1

To learn more about the syntax and structure of a help page so that you can create your own, check out:

help about_comment_based_help


If you run the following commands (one after another) on the command line:

get-service | Select-Object -First 5
get-process | Select-Object -First 5

And then repeat this from within a script. Then the output for get-service is like before, but get-process's output comes out in list format. 

That is because in a script, everything is outputed in a single instance of the pipeline, and the out-host is only able to get formating information once. That's why ite gets it for the first command that it recieves form the pipe. 

As result, out-host doesnt know how to format subsequent outputs so it ends up formating based on the format-list style of formatting. 

Because of this, the best practice is to only develop powershell scripts that only outputs one type of object, since scripts are limited to a single
pipeline. Howeever this causes small problem during script development, that is sometime for debugging and development, you will want to output some strings to check everything is going ok (e.g. echoing line numbers). Luckily you can do this using "write-verbose", this is a special case designed for this purpose. More about write-verbose later.   

When you run a linux shell script, the script's execution takes place in it's own shell. The same is kind of true for powershell scripts. However the 
terminology is a bit different. A scope is kind of container for containing things like aliases, functions, and variables. 

The shell itself is a top-level scope, and hence is called a "global scope". When a script is run, a new scope is created for the script 
and that scope is called the "script scope".  The script scope is a subsidiary of the global scope, and is often referred to as 
the child of the global scope. Whereas the global scope is referred to as the parent scope.  

Functions also get their own "private scope". 

A scope only lasts as long as you need to execute whatever is in the scope. Once a scope vanishes, it takes everything inside it with it. 

In linux, you have to pass all the variable into a script using parameters. If there is a variable in the script that hasn't been defined (although
it has been defined in the shell that is invoking the script), then the script can fail due to the undefined variable. This is not the case in 
powershell. However that is not th case in powershell. If a script requires a variable that hasn't been defined in the script scope, then it will 
start searching for it in it's parent scope, and keep looking upwards throught the scope chain until it finds the parameters value. In other words
a child scope is not encapsulated from it's parent scope. So to avoid confusion on where values are are coming from, you can:

	- Define all variables within the script (excluding script parameters)
	- Ensure all parameters have default values. 
	
With respect to functions, linux and powershell behaves in the same way in the sense that they both look within the script scope. However powershell
will move up to parents if it can't find what it wants in the script scope.   

However, luckily, you can do sourcing in powershell (by placing this line in the powershell script):

. c:\path\to\env\properties\script		# this script contains environment related variable settings.

The above acts like replacing the above line with all the content of the environment script, so that it is within the same script scope.