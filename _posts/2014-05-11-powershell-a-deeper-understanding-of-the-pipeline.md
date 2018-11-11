---
ID: 63
post_title: 'PowerShell &#8211; A deeper understanding of the pipeline'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-a-deeper-understanding-of-the-pipeline
published: true
post_date: 2014-05-11 00:00:00
---
Chapter 9 - A deeper understanding of the pipeline


When piping data from one command to the next, there are times when you need to help the receiving command understand what data it is receiving from the inputing pipe. This is done by forwarding the piped data to a command's particular parameter. This concept is known as "pipeline parameter binding". By default, powershell will automatically try to pipe to a command using the first method (aka "ByValue") and if that fails then it will then attempt the 2 method (aka "ByPropertyName"). Hence the powershell makes up to 2 attempts in the following order:

1. Byvalue			(based on object compatibility)
2. ByPropertyName    (based on property names, might need to create a hashtable to simulate the match)
3. Feed data directly into a parameter (e.g. an array using something like $(get-content computerlist.txt))

Plan A - Byvalue 
When you do a "get-member", you will find something called "typename" near the top. This basically defines an object to belong to a specific object type.

For example if you want to find out whether get-process can feed into stop-process, first do:

Get-Process | gm | Select-Object -First 10


the above will display the typename to be:

	TypeName: System.Diagnostics.Process

You can also find this in the full help page, i.e. do "help get-process -full", and then go down to the "INPUTS" section:

	INPUTS
		System.Diagnostics.Process
			You can pipe a process object to Get-Process.
  
Now see the full help for stop-process, and in the input section you will find:

	INPUTS
		System.Diagnostics.Process

You can pipe a process object to Stop-Process. 

However the above only tells you what types of objects (i.e. typenames) it can accept from the incoming pipeline, it doesn't actually explain what it does with the input it receives.  To find this out, you need to view the command's full help and look up the description for the 
is for the "InputObject" parameter:

InputObject <Process[]>						# Here, "process" is actually a name of a typename (i.e. object type). 
	Stops the processes represented by the specified process objects. Enter	a variable that contains the objects, or type a command or expression that
	gets the objects.

	Required? true
	Position? 1
	Default value
	Accept pipeline input? true (ByValue)
	Accept wildcard characters? false

This means that this command can accept any object of the type "process".

	

I think that all commands that can accept an input, has a parameter called "InputObject", which tells you which parameter is mapped to incoming
piped data. So if data is different to what it is expecting then it will not work. If there is a mismatch using the byvalue system, then ps will try then try to do complete piping using Plan B:


  
Plan B: piping using ByPropertyName
This occurs when plan A fails. It basically looks for a name-match between an outgoing property's (column) name and the receiving command's parameter name. E.g. if command A generates a collection (table) with a property (column) called "name", and command B has a parameter with the name "-name", then it will pipe it to that parameter. 

However this will fail if the values within the property is a different context to what -name was expecting. 

What if Plan B fails!
If you know that a property should be directed to a certain parameter, but isn't working because
the property's name and parameter's name don't match, in this case you can make some configurations to 
do a manual mapping instead. This is done by creating a hash table:

http://en.wikipedia.org/wiki/Hash_table 

http://www.howtogeek.com/114344/5-cmdlets-to-get-you-started-with-powershell/ 

Lets assume we have command1 and command2 which both have a property called "ServiceName", where command2 can also accept "ServiceName" by ByPropertyName, in this case the following will successfully pipe:

command1 | command2


Now if you we have Command3 (a command which outputs column "name") | Command4 (a command where property "servicename" can accept by ByPropertyName)....then the following will not work:

command3 | command4


To fix this, set up manual mapping using the select-object command:

CommandA | 
select-object -property *,
@{name='service' ; expression={$_.servicename}},
@{name='B1' ; expression={$_.B}}

Notice here, that we used commas to break down a single command line into several lines to improve 
readability. 

Here is an example:

Get-Service | 
select-object -property *,
@{name='Sher-name' ; expression={$_.Name}},
@{name='Sher-stopper' ; expression={$_.Displayname}}

In this example, our custom property names are "Sher-name" and "Sher-stopper".


Note, the expression between the curly brackets can be a command, a whole script, or a whole scripts filename. 
In this case we used an existing value in the pipeline using the placeholder "$_". Hence something like
$_.B will retrieve the current pipeline object, but only the part under the B property (column). Hence $_.B
effectively pulls out single entry from a row. 

The "$_" houses the current pipelined object (row). Also the period right after it basically is our way of telling PS that we are focussing on one 
entry (property) in the row, which we identify by the property's name. 

http://stackoverflow.com/questions/3494115/what-does-mean-in-powershell 

You can think of it as one of ps's special variable, like $? is a special variable (but for exit code) in linux.
For linux, the closest thing to this could be in a loop where we do "for row in `ls -l`; do;....done". Also the latest
version of bash can now do hash tables:

http://stackoverflow.com/questions/1494178/how-to-define-hash-tables-in-bash



Sometimes, you will want to pipe into a parameter which (according to full help) will not accept piped in data regardless of whether we are trying to pipe-in matching typenames (byalue), or doing it by matching linking properties (ByPropertyName). One way to overcome this is by rewriting the command to feed data into the parameter in another way. 



e.g. The -computername parameter for get-wmiobject doesn't accept piped data (although it can accept an array), 
hence the following won't work:

get-content .\computers.txt | get-wmiobject -class win32_BIOS (the text file only contains a list of computer names)

However the work-around for this is by doing this:

get-wmiobject -class win32_BIOS -computername (get-content .\computers.txt)

Note, in the above we just read from a file, however you might want to get content from another commands output. 
In this case, just take out single property:

get-service -computername (get-process | select-object -property name)

This above command is made up anyway, however there is another reason it work work, the select-object's property command
outputs the data in a tabular form, including heading, whereas we want it in array form, to fix this, simply use the 
-expandproperty option instead:

get-service -computername (get-process | select-object -expandproperty name)
 
 
You can think of "select-object -property" as selecting a box out of a number of boxes, 
whereas "select-object -expandproperty" takes it one step further and takes the content out of the box, and 
throws away the box.

Note: the -expandproperty can give a a further