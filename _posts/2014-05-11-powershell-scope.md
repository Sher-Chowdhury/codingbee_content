---
ID: 80
post_title: 'PowerShell &#8211; Scope'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-scope
published: true
post_date: 2014-05-11 00:00:00
---
Chapter 5 - Scope

Note: in csharp, even an if-else statement is it's own scope. See if this is also true in powershell. 

Scope is a containerization system designed to stop things from conflicting with each other. 
  
If you define a variable in a script, and then redefine it within a function that resides in the script, then that variable has only been redefined within the function and not in the script itself. 

Sometimes you will want to define a variable in a function and you want it to become available in the parent's scope. This is a useful way to get 
an output of a function to be used as part of the rest of the script, This is done using the new-variable command, here is the script content in this example:


"Current output is $output"
"Current output is $output1"
function hello {
    new-variable -Name output -Value 'Microsoft' -scope 1	# The "-scope" makes $output become available in current scope and one scope above.  
    new-variable -Name output1 -Value 'google'			# The value for "-scope" is zero, which is the current scope only.
    "The value of output within function is $output"
    "The value of output1 within function is $output1"
}
hello
"The value of output within script is $output"
"The value of output1 within script is $output1"


Running the above code will give:


PS C:\> .\test.ps1
initial output is 
initial output is 
The value of output within function is Microsoft
The value of output1 within function is google
The value of output within script is Microsoft		# As you can see "$output" is now present on the script level. 
The value of output1 within script is 

If you want to make a variable available everywhere, i.e. in all scopes then use the "global" value for the "-scope" parameter, i.e.:

new-variable -Name output -Value 'Microsoft' -scope global. 

Even sideway function can end up using this value for $output (by looking into parent(s) scopes), unless $output has already been defined within the function. 

If you want variable to exist just within the current scope, then you can omit defining "-scope", or do:

new-variable -Name output -Value 'Microsoft' -scope 0
new-variable -Name output -Value 'Microsoft' -scope local

A shorthand way to write global parameters, is:

PS C:\> $global:output = "Microsoft"		# This is similar to the linux variable export command.
PS C:\> $output
Microsoft

Note: There is also an "Allscopes", but not sure how this can be useful. 


You can also control how scopes operate using this command:

set-strictmode

This is a switch style command, and there are only 3 modes:

set-strictmode -off				# This is the default, where this feature is disabled. 
set-strictmode -version 2.0	# This mode will cause error messages if you try to use a variable that doesn't exist in current scope or in any 
							  parent scopes. 
set-strictmode -version 1.0	# Same as 2.0, except that it will ignore any undefined variables that are in double quotes. 

The version 2.0 above will be really useful for debugging purposes, and will stop you from running script where you have forgotten to define a 
variable.