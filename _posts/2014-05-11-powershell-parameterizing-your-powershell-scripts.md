---
ID: 74
post_title: 'PowerShell &#8211; Parameterizing your PowerShell scripts'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-parameterizing-your-powershell-scripts
published: true
post_date: 2014-05-11 00:00:00
---
Chapter 22

Previously we showed how you can pass parameters into a script using the param construct: 

param(
[string] $Environment = 'stress',			
[string] $Application = 'dropbox',
[switch] $RecommendedSettings = $true		
)

For the rest of this chapter we are going to cover some cool builtin scripting tools that you can use when developing shell scripts. 
For these to work you might need to enable these features by inserting the following line before 
the param section, (but after the help section, if any):

[cmdletbinding()]

for example like this:

[cmdletbinding()]
param(
[string] $Environment = 'stress',			
[string] $Application = 'dropbox',
[switch] $RecommendedSettings = $true		
)


Note, if you insert this, then you always have to include the param construct, if you're script doesn't require any parameters, then simply insert
and empty param construct "param()" 

If you want to make one of these paramaters mandatory (and hence has no default), then do:

param(
[string] $Environment = 'stress',
									# I added blank line for easier readability.
[parameter(mandatory=$true)]
[string] $Application,				# I have now made this mandatory. I did this by removing the default, 
									# and preceding the line with an attribute for this parameter. 

[switch] $RecommendedSettings = $true		
)
 
 
Now when a user runs the above script, they must either specify the "-applicaton" option on the command would. Otherwise they will get prompted
for the value by a pop up window (or in the command line).

You can also give a parameter an additional name, to make it easier for the user:

   
param(
[string] $Environment = 'stress',

[alias('software')]					# here we added an alias to the $application				
[parameter(mandatory=$true)]
[string] $Application,				

[switch] $RecommendedSettings = $true,

[ValidateSet(2,5)]			# Here is another cool feature, where we are saying that $version can only take the values of 2 or 5.
[int] $version = 2		
)
 
As a result, both of the following commands now do the same thing:

.\script.ps1 -application dropbox 
.\script.ps1 -software dropbox 
 
 
Another cool feature is using powershells builtin "-verbose" mode which you can use within a script. Within your script, type:

write-verbose: "Successfully connected to the remote machine." 
 
Then to view this message you simply type "-verbose" on the command line:

.\script.ps1 -application dropbox -verbose