---
ID: 75
post_title: 'PowerShell &#8211; Regular Expressions'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-regular-expressions
published: true
post_date: 2014-05-11 00:00:00
---
Note: chapter 23 was skipped, nothing useful

Chapter 24 - regular expressions

You can use an operator called "-match" which pretty much is the linux equivalent of grep. "-match" is referred to as an operator rather than a command. Here are some examples:

PS C:\> "hello" -match "el"
True
PS C:\> "hello" -match "^he"				# Matches pattern at the beginning of the line. Just like linux
True
PS C:\> "hello" -match "lo$"				# Matches pattern at the end of the line. Just like linux
True

PS C:\> "don" -match "d[aeiou]n"
True
PS C:\> "doon" -match "d[aeiou]n"
False
PS C:\> "doon" -match "d[aeiou]{2}n"	# "{2}" means match exactly 2 instances of the preceding character. 
True
PS C:\> "d_m9n" -match "d\w\w\wn"   	# "\w" matches a letter, or number or underscore.
True
PS C:\> "d-%(n" -match "d\w\w\wn"
False
PS C:\> "d-% n" -match "d\W\W\Wn"  		# "\W" Matches everything else that "\w" doesn't match. Even white space. 
True
PS C:\> "d-%2n" -match "d\W\W\Wn" 
False
PS C:\> "d n" -match "d\sn"  			# "\s" matches white(s)pace, e.g. space bar, tab, or carriage return.
True
PS C:\> "don" -match "d\sn"  # "\s"
False
PS C:\> "don" -match "d\Sn"  		# "\S" Matches everything else that "\s" doesn't match. 
True
PS C:\> "d n" -match "d\Sn"  		# "\S"
False
PS C:\> "d!%4Z n" -match "d.....n"  		# A period matches any letter, or number, or special character, or even whitespace!
True

PS C:\> "dns" -match "d[k-p]s"  # matches any letter that falls between k-p. 
True
PS C:\> "dxs" -match "d[k-p]s"   
False
PS C:\> "dxs" -match "d[k-p,w-z]s"  # matches any letter that falls between k-p, or w-z. 
True
PS C:\> "dxs" -match "d[^k-p]s"  	# "^", when used inside square brackets, this means match anything except for letters k-p. 
True
PS C:\> "don" -match "do?n"  		# "?" means matching 0 or 1 instance of the preceding character, which in this case is "o" 
True
PS C:\> "dn" -match "do?n"  
True
PS C:\> "doon" -match "do?n"  
False
PS C:\> "dn" -match "do{0,1}n"		# You can replace "?" with {0,1}
True
PS C:\> "dn" -match "do*n"  # "*" means matching 0 or more instances of the preceding character, which in this case is "o" 
True
PS C:\> "don" -match "do*n"  
True
PS C:\> "dooon" -match "do*n" 
True
PS C:\> "dooon" -match "do{0,}n" 	# You can replace "*" with {0,}
True
PS C:\> "doooon" -match "d[aeiou]+n"   #"+" means matching one or more instance of the previous character
True
PS C:\> "dn" -match "d[aeiou]+n"
False
PS C:\> "doain" -match "d[aeiou]+n"
True
PS C:\> "dotin" -match "d[aeiou]+n"
False
PS C:\> "doain" -match "d[aeiou]{1,}n"	# You can replace "+" with {1,}
True



PS C:\> "dn" -match "d[aeiou]+n"
False
PS C:\> "doain" -match "d[aeiou]+n"
True
PS C:\> "dotin" -match "d[aeiou]+n"
False
PS C:\> "dean" -match "d[aeiou]n"
False
PS C:\> "d9n" -match "d\dn"     		# "\d" means match any digit from 0-9
True
PS C:\> "d9n" -match "d\Dn"    # "\D" matches anything except digits. 
False
PS C:\> "de! *n" -match "d\D{4}n"   
True
PS C:\> "192.168.0.1" -match "\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}"	# Here is how you can grep for ip numbers. 
True

PS C:\> "d13n" -match "d\d+n"     
True
PS C:\> "dhellon" -match "d(hello)+n"	# This matches the block "hello" 1 or more times.  
True
PS C:\> "dhellohellohellon" -match "d(hello)+n"
True
PS C:\> "dhellohelllohellon" -match "d(hello)+n"	# You can also use blocks in the same way with "*", "?" and "{}"
False
PS C:\> "dn" -match "d(hello)+n"
False


help about_regular_expressions		# Here you can find more info about regular expressions. 

Note: all the above are not case sensitive, but you can do case sensitive by using "-cmatch".


Now you understand how regular expressions work, you can then use them in commands that accepts the "-match" operator, e.g.:

PS C:\> Get-Service | Where-Object -FilterScript {$_.Displayname -match "d[aeiou]n"}

Status   Name               DisplayName                           
------   ----               -----------                           
Stopped  MSDTC              Distributed Transaction Coordinator   


#####################################################################################
##### Detour regular expressions - start
#####

There is a special reserved variable, called "$matches" that stores the result of a regex query. 

e.g. 

Lets say we have the following string variable:

$component = "Bulk Update (BU)"

...and we want to pull out whatever is inside the round brackets, in this case we can do something like this:

PS C:\> $component -match '\(.+\)' 
True

However the actual matching portion is automatically stored in "$matches", which is special type of hash table:

PS C:\> $matches

Name                           Value                                                                                                    
----                           -----                                                                                          
0                              (BU)     

now you can pick out the value itself like this:

PS C:\> $matches[0]
(BU)

Also if you are just interested in the letters and not in the round brackets, you can do:

PS C:\> $matches[0].trimstart("(")
BU)

PS C:\> $matches[0].trimend(")")
(BU

Combining both together gives:

PS C:\> ($matches[0].trimstart("(")).trimend(")")
BU

# for more about trim, checkout : http://social.technet.microsoft.com/Forums/windowsserver/en-US/2576a409-6cc2-4772-bfb7-8c0b79b7c015/powershell-what-is-an-equivalent-of-substring-ltrim-rtrim-functions?forum=winserverpowershell

#####
##### Detour regular expressions - end
#####################################################################################
 

However if you want to search through files, then you can use regex in conjunction with the select-string command (along with the "-pattern" option): 

#first I created the file 
PS C:\Documents and Settings\SChowdhury\Desktop> "This is a testfile" > testfile
PS C:\Documents and Settings\SChowdhury\Desktop> "This is another line to experiment with greping" >> testfile
PS C:\Documents and Settings\SChowdhury\Desktop> "How about a third line." >> .\testfile

#Then checked the contents of the file
PS C:\Documents and Settings\SChowdhury\Desktop> Get-Content .\testfile
This is a testfile
This is another line to experiment with greping
How about a third line.

# Now greped the file
PS C:\Documents and Settings\SChowdhury\Desktop> Get-Content .\testfile | Select-String -Pattern "[ml]ine"
This is another line to experiment with greping
How about a third line.

This technique is useful if you want to search through a log file. However windows has lots of log files, and if you don't know which log file 
to look in then can do:

# First here are 2 log files that I created which have different names and stored in different directories:

PS C:\> Get-Content "Documents and Settings\SChowdhury\Desktop\testlogfile.log"
this is a test log file a c
this is a test log file def
this is a test log file abc

PS C:\> Get-Content "Documents and Settings\SChowdhury\My Documents\Downloads\bestlogfile.log"
this is a test log file a!c
this is a test log file def
this is a test log file a9c

# Now do the search to recursively check each directory in the c drive for a file name ending with "logfile.log" and then for each file
# found, return the lines which has a regex match for "a.c":

PS C:\> Get-ChildItem -path c:\ -Filter *logfile.log -Recurse | Select-String -Pattern "a.c"

Documents and Settings\SChowdhury\Desktop\testlogfile.log:1:this is a test log file a c
Documents and Settings\SChowdhury\Desktop\testlogfile.log:3:this is a test log file abc
Documents and Settings\SChowdhury\My Documents\Downloads\bestlogfile.log:1:this is a test log file a!c
Documents and Settings\SChowdhury\My Documents\Downloads\bestlogfile.log:3:this is a test log file a9c

Notice that the output is columns (properties) that are colon delimited, they are:

	1. Full path of the file
	2. Line number where a match has been found
	3. The actual line itself
	
You can format the output by first using get-member to find the property names, and then output the properties that you are interested in using format-table, in my case that is:

PS C:\> Get-ChildItem -path c:\ -Filter *logfile.log -Recurse | Select-String -Pattern "a.c" | Format-Table -Property path,linenumber,line -wrap

Path                                                               LineNumber Line                                                    
----                                                               ---------- ----                                                    
C:\Documents and Settings\SChowdhury\Desktop\testlogfile.                   1 this is a test log file a c                             
log                                                                                                                                                                        
C:\Documents and Settings\SChowdhury\Desktop\testlogfile.                   3 this is a test log file abc                             
log                                                                                                                                                                        
C:\Documents and Settings\SChowdhury\My Documents\Downloa                   1 this is a test log file a!c                             
ds\bestlogfile.log                                                                                                                                                         
C:\Documents and Settings\SChowdhury\My Documents\Downloa                   3 this is a test log file a9c                             
ds\bestlogfile.log                                                                                                                                                         


So far we saw how use regexes in a couple of ways, here is a more complete list:

	- Where-Object -FilterScript (using the "-match" or "-cmatch"operator)
	- switch 	(this is used in scripting, similar to linux's case statement I think)
	- Select-string (using the "-pattern" option)
	

	
	
cd $home 		# this is the linux exquivalent of "cd ~". 	
	























-- detour

-
- get-content textfile.txt # equivalent to linux's "cat" command
-
-- detour

When using get-help, you can use the example switch to just view the examples section of the full man page:

get-help get-eventlog -examples

Here is a quick way to do a regular expressions (regex) on lists:

get-help * | where-object {$_.name -match "about"}

Alse see this example
Get-ChildItem | Where-Object -FilterScript {$_.name -match "fmeEngineConfig_[0-9]{1,}.txt"}
# the regex here has "{1,}" which means find one or more matches of the preceding regex character. SO this will return things like:

Mode LastWriteTime Length Name
---- ------------- ------ ----
-a--- 08/01/2014 11:37 20947 fmeEngineConfig_1.txt
-a--- 08/01/2014 11:37 20947 fmeEngineConfig_10.txt
-a--- 08/01/2014 11:37 20947 fmeEngineConfig_2.txt
-a--- 08/01/2014 11:37 20947 fmeEngineConfig_100.txt

But it will not return things like:
Mode LastWriteTime Length Name
---- ------------- ------ ----
-a--- 08/01/2014 11:37 20947 fmeEngineConfig.txt
-a--- 08/01/2014 11:37 20947 fmeEngineConfig_.txt

Also see this for more examples: http://technet.microsoft.com/en-us/library/ff730947.aspx

Here are the standard regular expression (regex) operators:

? The preceding item is optional and matched at most once.
* The preceding item will be matched zero or more times. Here, the * is not treated like a wildcard.
+ The preceding item will be matched one or more times. NOTE: This doesn't actually work in powershell, so use "{1,}" instead
{n} The preceding item is matched exactly n times.
{n,} The preceding item is matched n or more times.
{,m} The preceding item is matched at most m times.
{n,m} The preceding item is matched at least n times, but not more than m times.

Here, we are applying a filter on the table of contents displayed by the "get-help" command, and filtering based on the "name" column, where there is a pattern match for the word "about". In linux, we can achieve the same result by using the awk command for identifying the correct column, and then use grep.

Hence the "where-object" command acts as a combination of both grep and awk. Here is another way to get the desired effect (using wildcards):

get-help about* # This lists help topics that are not specific to any command.




# [regex] $buildnumber = "_[0-9][0-9][0-9][0-9]$"

# $VersionToBeDeployed = $Version -replace "$buildnumber","" -replace "comp_BLD_",""

# Retrieves full name of comp currently deployed.