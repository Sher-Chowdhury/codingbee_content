---
ID: 76
post_title: 'PowerShell &#8211; Additional random tips tricks, and techniques'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-additional-random-tips-tricks-and-techniques
published: true
post_date: 2014-05-11 00:00:00
---
Chapter 25 - Additional random tips tricks, and techniques

In Linux, you have the .profile script in your home directory. This is where you can place various commands and settings that get set up when you 
start a new shell (e.g. set up new aliases too). You can also do the same thing in ps, however you have to take extra care, because ps has two hosting applications that accesses the ps engine, the console host and the ISE host.  



For more info see:

help about_profiles


In ps, there are a number of profile files (some or all of which you might need to create yourself), and these script are run in the following order:

	1. $pshome/profile.ps1  							# This will execute for all users and all hosting application. 
	2.  - $pshome/Microsoft.Powershell_profile.ps1  	# This will execute for all users  but only if they are running the console host. 
		- $pshome/Microsoft.PowershellISE_profile.ps1  	# This will execute for all users but only if they are running the ISE host.
	3. $home/Document/WindowsPowerShell/profile.ps1		# This will execute for the current user only and on all hosts. 
	4. - $home/Document/WindowsPowerShell/Microsoft.Powershell_profile.ps1   # This will execute for the current user only but only for the PS console 
	   - $home/Document/WindowsPowerShell/Microsoft.PowershellISE_profile.ps1   # This will execute for the current user only but only for the PS ISE  
	
If any of the above don't exist then ps will simply skip to the next one. 



Now we take a look at operators, which will make more sense by looking at some examples:


PS C:\> 1001 / 3
333.666666666667
PS C:\> 1001 / 3 -as [int]		# The "-as" operator does it's best to convert one data type to another. 
								# In this case it converts this decimal to an integer by rounding (up or down) to the nearest digit. 
334
PS C:\> $number = 1001 / 3 -as [int]
PS C:\> $number
334

Other available types are:
		[int]
		[xml][/xml]
		[string]
		[datetime]
		[single]
		[double]
		....and others. but these are the main ones. 
		
PS C:\> 1001 / 3 -is [int]		# The "-is" operator is a check to see if an object is a certain type or not.  
False
PS C:\> 1002 / 3
334
PS C:\> 1002 / 3 -is [int]
True	
	

	
PS C:\> "hello world" -replace "hello","goodbye"
goodbye world

The "-replace" operator does the same thing as linux's "sed s/.../.../" command. 

If you want to do this to a whole file (i.e. the (g)lobal option sed s/.../.../g), then do:

PS C:\> Get-Content .\testfile.txt
hello world
also hello to the universe. 
PS C:\> (Get-Content .\testfile.txt) -replace "hello","goodbye"
goodbye world
also goodbye to the universe. 


Now lets look at some operators that are designed for arrays. 

PS C:\> [array]$array = @()  # This creates an empty array

PS C:\> [array]$array = 'one','two','three','four'
PS C:\> $array
one
two
three
four
PS C:\> $array -join "-"	# The "-join" operator joins all the array items into a string: 
one-two-three-four 
PS C:\> $newvar = $array -join "-"
PS C:\> $newvar 
one-two-three-four	

	
PS C:\> $alphabet="a b c d"
PS C:\> $alphabet
a b c d
PS C:\> $newarray = $alphabet -split " "
PS C:\> $newarray
a
b
c
d
$newarray[2]

If you have an array that contains a lot of list items, and you are looking if it contains a particular array item, then you can view them like this:

PS C:\> $collection = 'abcd','efgh','ijkl'
PS C:\> $collection 
abcd
efgh
ijkl
PS C:\> $collection -like "*fg*"		# "-like" will return results and lets you use wildcards. It is similar to "-match"
efgh
PS C:\> $collection -contains "efgh"	# "-contains" is used when you know exactly what you are looking for. It doesn't let you use wildcards. 
True
	
	
# Now we'll take a look at string manipulation. 

If you do a gm on a string, you will find it has a lot of methods. Here are some examples:

PS C:\> $testvar = "1234x678"
PS C:\> $testvar.IndexOf("x")	# The "IndexOf" method tells you the location of a given character.
4								# The position counter starts from 0 and not 1.

PS C:\> $testvar.split("x")		# works similary to the "-split" operator. 
1234
678 

PS C:\> $testvar
1234x678
PS C:\> $testvar.replace('x','z') 	# works similarly to the "-replace" operator. 
1234z678
	
	
PS C:\> $testvar = "   abcDEF "
PS C:\> $testvar.toupper()		# changes whole string to upper case
   ABCDEF
PS C:\> $testvar.tolower()		# changes whole string to lower case
   abcdef
PS C:\> $testvar.trim()			# removes any whitespace. There is also trimend() and trimstart() which involves rempving whitespaces from either side.
abcDEF

("AbC").toupper()			# you can also pass a string directly into a method, rather than storing it in a variable first. 
ABC


 Another useful command is the command that outputs the date:
 
PS C:\> get-date
14 August 2013 08:12:04
	
If you view this command's methods, you will find that you can manipulate the outputting object in the following ways:

(get-date).month			# Notice that to use this method we had to put the main command in brackets.
8	
	
	
$currentDate = get-date			# you can also do the store-object-in-variable-first approach. 
$currentDate.adddays(-90)		# gives you the date that was 90 days ago. 
16 May 2013 08:31:18	

PS C:\> $currentDate.ToShortDateString()		#This method used the machine's current regional settings before determining the appropriate format. 
14/08/2013


ToShortDateString() is a method that is available with all wmiobjects. 
 

a lot of wmi objects have date and time info. However, the date/time format is difficult to read:


 
PS C:\> $os.converttodatetime($os.lastbootuptime)
05 August 2013 08:06:26 

 
 
PS C:\> Get-WmiObject win32_operatingsystem | Select-Object -Property lastbootuptime
lastbootuptime                                                                                                                                                             
--------------                                                                                                                                                             
20130805080626.375000+060 
 
 
Fortunately ALL wmi objects have a ConvertToDateTime method, to transform the date/time into something that's easier to read:

PS C:\> (Get-WmiObject win32_operatingsystem).convertToDateTime((Get-WmiObject win32_operatingsystem).lastbootuptime) 
05 August 2013 08:06:26 

I have rewritten the above command into a slightly more easier to read format: 
PS C:\> $wmiobject = Get-WmiObject win32_operatingsystem 
PS C:\> $wmiobject.converttodatetime($wmiobject.lastbootuptime)
05 August 2013 08:06:26
 


In powershell, many commands have parameters that have default values. For example "get-childitem" has the optional "-path". This is optional 
because it has a default (which turns out to be the current directory). 

In PS you can define your own defaults for any parameter in any command. These defaults are stored in a 
special built-in variable called called $PSDefaultParameterValues. This variable is designed to store a hash table. 

By default this variable is empty when you start a new shell window, but you can fix that by defining the $PSDefaultParameterValues in the profile script.  

For example in PSv3, you can create a credential object that stores your username/password using:

get-credential		# This commands opens up a popup windows prompting for username/password. After that a credential-object is created/. 

This object can then be fed into any commands that has a "-credential" parameter.     


So here is how to get a new default added to the $PSDefaultParameterValues (in this case for the -credential parameter):

$MyLoginDets = get-credential -username admin -message "please enter your password"

Now you need to link (pair) $MyLoginDets with the -credential (using hash table), and then feed that into $PSDefaultParameterValues using the buitin variables's "add" method:

PS C:\>  $psdefaultparametersvlaues		# currently empty
PS C:\>  $psdefaultparametersvlaues.add('*:credential',$logindets)   
PS C:\>  $psdefaultparametersvlaues

Name							Value
----							-----
*:credential					system.management.automation.pscredential

The asterisk means that this is the default value for all commands. However if you only want to use this as the default for "invoke-command", 
then do:

$psdefaultparametersvlaues.add('invoke-command:credential',$logindets)

Also in psv3, for more info check out:

help about_parameters_default_values		



## Playing with script blocks

scripts blocks are basically a block of commands that you can embed within another command. The block is indicated by curly braces {...}.

Note: hash table syntax is @{...}, this is an exception to the above rule since these curly brackets will hash table data and not commands. 

scriptblocks are used in several places:

	- with where-object's -filterscript parameter (in fact it is a mandatory parameter here).
	- with foreach-object's "-process" parameter (in fact it is a mandatory parameter here).
	- Inside of hash tables (e.g. to convert bits to megabytes)
	- Commands that accepts the "-scriptblock" command, e.g. "invoke-command" and "start-job"

for more info, check out:

help about_script_blocks