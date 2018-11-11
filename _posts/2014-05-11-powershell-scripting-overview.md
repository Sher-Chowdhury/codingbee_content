---
ID: 78
post_title: 'PowerShell &#8211; Scripting overview'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-scripting-overview
published: true
post_date: 2014-05-11 00:00:00
---
Chapter 2 and 3 - Powershell scripting overview

You will notice that a ps script's extension is ".ps1" and not. The "1" indicates the version of ps's scripting 
language (which hasn't really change much since ps first came about) as opposed to the version of ps itself. 

You should try using ps ISE for writing/editing scripts rather than notepad++. That's because of the autocomplete feature. 


Chapter 3 - Powershell's scripting language

Everything that's outputted by a script actually runs through a single pipeline. That means that if the script is outputting several types of outputs 
then the subsequent outputs after the first one is outputted in format-list type. 

The following is also sent through a single pipeline:

get-service;get-process 

That's why it is best practice to design ps scripts that only outputs one type of object.



when you store an object like this:
$var = get-process

Then this object is actually storing a collection of objects, since each row is an object. There you can view a specific object like this:

PS C:\> $var[0]
Handles  NPM(K)    PM(K)      WS(K) VM(M)   CPU(s)     Id ProcessName                                                                                                      
-------  ------    -----      ----- -----   ------     -- -----------                                                                                                      
     84       3     1208       3404    32     2.31   3524 alg                                                                                                              

PS C:\> $var[1]
Handles  NPM(K)    PM(K)      WS(K) VM(M)   CPU(s)     Id ProcessName                                                                                                      
-------  ------    -----      ----- -----   ------     -- -----------                                                                                                      
    125       6     8332      10468    73   471.50    476 cagent32                                                                                                         


You can even drill down further to get column entries like this:

PS C:\> $var[0].processname
alg
PS C:\> $var[1].processname
cagent32
PS C:\> $var[0].vm
33480704

In effect, you can think of this variable as an array of objects. 

Now, if you try this:

PS C:\> "The first process is using up $var[0].vm of memory"   	# This will actually fail because the double quotes causes confusion. 

Hence the correct way to do this is by using the $(...) construct like this:

PS C:\> "The first process is using up $($var[0].vm) of memory"
The first process is using up 33480704 of memory

The $(...) is essentially the linux equivalent of the back-ticks: `...`.  Anything inside the $() gets treated as code and is evaluated first 
before the rest of the command is evaluated. 


PS C:\> "The first process is using up $("a lot of") of memory"
The first process is using up a lot of of memory 


You also have "`" for escaping characters:
PS C:\> "Hello my name is: `tSher `nI have `$20 in my wallet"
Hello my name is: 	Sher										# "`t" this create a tab. "`n" indicates new line (i.e. return carriage). 
I have $20 in my wallet											# the "`" used here caused the $ sign to be treated as a literal character. 

Note, in the above, we gave special special meanings to "t" and "n", but took away the special meaning for "$" using the back-tick character.

The linux equivalent to "`" is the "\".

Going back to an earlier example:



PS C:\> $var[1]
Handles  NPM(K)    PM(K)      WS(K) VM(M)   CPU(s)     Id ProcessName                                                                                                      
-------  ------    -----      ----- -----   ------     -- -----------                                                                                                      
    125       6     8332      10468    73   473.58    476 cagent32                                                                                                         
PS C:\> $var[1].processname												# see note 1 
cagent32

# note 1 - The period, "." after a variable name means....."I don't want to access the entire object with this variable, I just want to access 
# just one of it's properties or methods". After the period, provide a property or method name. Note: method names are ALWAYS followed by ().

Some methods accepts arguments, in which case you place them in the round brackets, comma delimited style. Other methods, don't require in which 
case you live the round brackets empty. Hence, here is an example:    

IMPORTANT: As mentioned a few lines above. You can access a variable's (which can also store objects, in fact a variable is an object) method/property by typing a period (.) followed by the properties or methods name. Note, method names are ALWAYS followed by "()", which makes it 
easier distinguish whether you are accessing a property or applying a method. Also when dealing with methods, the "()" can contain parameter values. 

Also note that you cannot use the above (period) technique on a "collection". But the purpose of this technique is to use it on a specific object:   


PS C:\> $myservices = Get-Service		# $myservice is a "collection" type object, essentially it is an array of objects.
PS C:\> $myservices[0]				# now we are looking at specific object (that is in the array)
Status   Name               DisplayName                           
------   ----               -----------                           
Stopped  Alerter            Alerter        

PS C:\> $myservices[0].name			# Now we are accessing a specific property within the object. 
Alerter

Important: here is a really cool thing, a property (e.g. $myservices[0].name) can itself be an object!!! This means you can use/access a property's 
method/property:

$myservices[0].name	| gm		# This will list all the availablee methods and properties for the "name" property  

The above lists a propery called "length" and a method called "substring", which you can try out:

PS C:\> $myservices[0].name.length 
7


$myservices[0].name.substring(2)	# Notice we are accessing a method as indicated by the round brackets. This method removes the first "x"
erter								# (in this case 2) characters and returns the rest. 		

Even the output of a method can be an object in itself:

PS C:\> $myservices[0].name.substring(2).length
5

The above is a really important concept that we will be using regularly!! 


PS C:\> $name | format-list -Property length
Alerter
PS C:\> $name.length			# not sure what is happening here, should display "Alerter"
7
PS C:\> $name.ToUpper()
ALERTER


Round brackets indicates the order of execution:

$name = (get-service)[0].name		# first, the round bracket gets replaced by a collection type object. 
									# Then we pull out the first object, then pull out the name properties of that object.  

get-services -computername (get-content machinenames.txt)	#assume text file contains one pc name per line. 

									
You can pass a condition result directly into an "if" statement

$status=$true		# Note, "$true" is a reserved variable of the object type boolean. Do "$true | gm" to confirm. Same is true for $false.
if ($status)
{
    Write-Host "The status is positive"		#This line will get executed. 
}
else
{
    Write-Host "The status is negative"
}



 
We also have the switch construct (the linux equivalent to this is the "case" statement): 

$status = "hot"
switch -wildcard ($status)			# you need to enable "-wildcard" in order to allow regex based matches. 
{ 
    0 {$status_meaning = 'ok'}
    1 {$status_meaning = 'error'}
    2 {$status_meaning = 'timed-out'}
    "h*" {$status_meaning = 'over-heated'}
    default {$status_meaning = 'Something really bad has gone wrong!!!'}
}
"Hear is the status meaning: $status_meaning"


The above will output:
Hear is the status meaning: over-heated

That's because "hot" is matched by the h* regex, and hence this sets the value for the $status_meaning.


In ps, you can concatenate strings: 

PS C:\> [string]$datetime = get-date
PS C:\> $message = "- INFO: Batch job completed successfully."
PS C:\> $logentry = $datetime + $message			# Here we concatenate two existing string variable to create a new string variable.  
PS C:\> $logentry
08/15/2013 15:48:07- INFO: Batch job completed successfully.
PS C:\> $logentry += " So no need to panic :o)"			# Here we use "+=" to append to an existing variable . 
PS C:\> $logentry
08/15/2013 15:48:07- INFO: Batch job completed successfully. So no need to panic :o)


Here is another switch example:

$servername=DCFILE
$result = ""
switch -wildcard ($servername)
{ 
	"*DC*" 	 {$result += 'Domain Controller'}
	"*FILE*" {$result += 'File Server'}
	"*SQL*"  {$result += 'SQL server'}
	"*EXCH*" {$result += 'exchange server'}
}
$result 

The above will output:

Domain ControllerFile Server


That's because there are 2 regex matches in the switch. and since we used "+=" rather than =, it ended up appending rather than overwriting.
If you want to exit after the first match (which I think happens in linux, but configurable in korn), then simply insert the "break" keyword:



$servername=DCFILE
$result = ""
switch -wildcard ($servername)
{ 
	"*DC*" 	 {
			   $result += 'Domain Controller'
			   break
			 }								# This time will exit switch block as soon stuff inside curly braces is run. 
	"*FILE*" {
			   $result += 'File Server'
			   break
			 }
	"*SQL*"  {
			   $result += 'SQL server'
			   break
			 }
	"*EXCH*" {
			   $result += 'exchange server'
			   break
			 }
}
$result 

The above will now output:

Domain Controller



test-path /path/to/folder		# this command checks if path to folder exists. Will either output true or false. 



Now lets take a look at looping constructs. Here is the construct for the do-while loop:

$counter=0
while ($counter -lt 6)  {
	"Hello counter is now equal to $counter"
	$counter++
}


Here is the while loop:
$counter=0
while ($counter -lt 6)  {
	"Hello counter is now equal to $counter"
	$counter++
}

By the way, you can do the linux equivalent of "&&" like this:

http://stackoverflow.com/questions/3936518/powershell-how-to-check-for-multiple-conditions-folder-existence 



Next we have the foreach loop:

$services = get-service | Select-Object -First 5			
foreach ($ServiceItem in $services) {
    $ServiceItem.name    					# one of the strengths of the foreach construct is that it will go through a table (object) 
											# and process each row (object) in turn.
}


Here is another way to write the above loop,  but this time using the "foreach-object" cmdlet:

get-service | Select-Object -First 5 | ForEach-Object -process {	# "-process" is a mandatory positional parameter, to accept scriptblock that are inside the curly brackets. 
	$_.name															# The $_ syntax represents the current piped in object. 
}


Note, in both foreach and foreach-object loops, you can skip to the next loop, but you use a different trigger word, in foreach, you use the traditional "continue" statement. But in "foreach-object" you use "return", that's because foreach-object is actually a cmdlet. 

The linux equivalent of the foreach loop (or the foreach-object construct) is something like "for file in `ls -l`" 
 
In Powershell, we use the term "enumerate" to refer to going through a list of items one at a time.  
 
 
Here is the for-loop construct (which is used to repeat a code block a set number of times):

for ($i=1; $i -le 5; $i++) {
	"This is line $i"
}
 
The above will output:

This is line 1
This is line 2
This is line 3
This is line 4
This is line 5


 You can replicate the for-loop construct using the foreach construct instead:
 
 1..5 | foreach-object -process {
 "This is line $_"
 }							# Note the "break" statement doesn't work here. It ends up exiting the whole script. Need to 
							# investigate if same also happens in PSv3. 
 
 Notice here that you don't need to set up a counter like $i. However this construct does have a built-in counter called "$_". 
 
 
 There are 2 keywords you can use to control loops:
 
	- Break   		(this is to prematurely exit a loop, or other constructs such as switch-statements or if-conditions)
	- Continue 		(this is to skip to the next iteration of a loop)
 
The "continue" keyword can only be used within a loop, but the "break" statement can be used in other places, e.g. in a switch statement, as shown 
earlier. If "break" is used  
 
 
If the "break" command is placed inside of a script, but outside of a construct (i.e. loops, if-conditions, or switch statements), then it will exit
the whole script when it is executed. The "break" command is the linux equivalent of the "return" command. 

PS also uses the "return" but this will exit the whole script regardless of where you place. In other words, the PS's "return" command is the
linux equivalent of the "exit" command. 

PS also uses the "exit" command. In this case, in PS, the "exit" and "return" both do the same thing, and you can change them interchangeably. However return can output a final string as part of the output. E.g. :

return "goodbye world"		# the goodbye    

However, I believe the best practice should be never use "return" on powershell, and instead just use "break" to exit out of a construct, and "exit"
to exit out of a whole script.