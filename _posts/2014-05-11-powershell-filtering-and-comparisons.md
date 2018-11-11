---
ID: 65
post_title: 'PowerShell &#8211; Filtering and Comparisons'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-filtering-and-comparisons
published: true
post_date: 2014-05-11 00:00:00
---
Chapter 11 - Filtering and comparisons

There is basically 2 approaches to filtering a command's output:

1. Using wildcards
2. Using the where-object command. 

1. Using wildcards

e.g.:

get-service -name e*,*x*  	# returns anything where the name property either begins with "e" or contains the letter "x"



Here are some comparisons done on the command line:

PS C:\> 5 -eq 5
True
PS C:\> 5 -eq 2+3
True
PS C:\> 5 -eq 2+4
False
PS C:\> 5 -ne 5
False
PS C:\> 5 -ne 6
True
PS C:\> 5 -gt 3
True
PS C:\> 5 -gt 5
False
PS C:\> 5 -ge 5
True
PS C:\> 'test' -eq 'TEST'     #Notice that it isn't case sensitive
True
PS C:\> 'test' -ceq 'TEST'		# The "c" means that it is case sensitive. 
False
PS C:\> 'test' -ceq 'test'
True
PS C:\> 'test' -cgt 'test'
False
PS C:\> 't' -cgt 'to'
False
PS C:\> 'to' -cgt 't'
True
PS C:\> 5 -eq 5 -and 4 -gt 3 					# This is equivalent to linuxes && operator
True
PS C:\> 5 -eq 5 -and 4 -eq 3 
False
PS C:\> 5 -eq 5 -or 4 -eq 3 
True
PS C:\> 5 -eq 4 -or 4 -eq 3 
False


PS C:\> 'hello' -like 'hello'
True
PS C:\> 'hello' -like '*ll*'
True
PS C:\> 'hello' -like '*LL*'
True							#notice it is case insensitive. use "clike" to make it sensitive.
PS C:\> 'hello' -clike '*LL*'
False
PS C:\> 'hello' -like '*pp*'	# the key thing about like is that it accepts wild cards. 
False
PS C:\> 'hello' -notlike '*LL*'
False
PS C:\> 'hello' -notlike '*xx*'
True
PS C:\> 'hello' -cnotlike '*LL*'
True
PS C:\> 'hello' -cnotlike '*ll*'
False


PS C:\> 'hello' -match '[a-z]*'						# The Match operator is where you can use regular expressions.
True

PS C:\> 'hello' -match '[o-z]'
True
PS C:\> 'hello' -match '[p-z]'
False



PS C:\> 'hello' -match '[a-z][a-z][a-z][a-z][a-z]'
True
PS C:\> 'hello' -match '[a-z][a-z][a-z][a-z][a-z][a-z]'
False

PS C:\> 'hello' -match '[A-Z]'		# Notice it isn't case sensitive
True
PS C:\> 'hello' -cmatch '[A-Z]'		# used the c option to now make it case sensitive.
False
PS C:\> 'hello' -cnotmatch '[A-Z]'
True


Also checkout:
help about_comparison_operators



All of these above comparison operators are most often used in conjunction with PS's main generic filter command, which is called "where-object" 
Here are some examples (taken from the help pages):

C:\PS>get-service | Where-Object -FilterScript {$_.Status -eq "Stopped"}
  
C:\PS>get-process | Where-Object -FilterScript {$_.workingset -gt 25000*1024}
   
C:\PS>get-process | Where-Object -FilterScript { $_.ProcessName -match "^p.*" }