---
ID: 71
post_title: 'PowerShell &#8211; Variables and Objects'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-variables-and-objects
published: true
post_date: 2014-05-11 00:00:00
---
You can store data in the form of a variable and then retrieve that data by calling the variable as and when needed. 

You can do all sorts of things with variables, here's a just a tiny examples.

Here we store a string in a variable:


<pre>PS C:\> $var1 = "CodingBee"
PS C:\> $var1
CodingBee
PS C:\> "Welcome to $var1"
Welcome to CodingBee
PS C:\> $WelcomeMessage = "Welcome to $var1"
PS C:\> $WelcomeMessage
Welcome to CodingBee
</pre>

You can also use variables to do maths:


<pre>PS C:\> $x = 2
PS C:\> $y = 8
PS C:\> $x + $y
10
PS C:\> $sum = $x + $y
PS C:\> $sum
10
PS C:\></pre>



Here are some examples of arrays:

<pre>
$array = 'item1','item2','item3'
PS C:\=> $array
item1
item2
item3

PS C:\=> $array[1]
item2

PS C:\=> $array[0]
item1

PS C:\=> $array[2]
item3

PS C:\=> $array[-1]
item3 
</pre>

In PowerShell, things like strings, integers, and arrays, are not just "data type", are not just data, they are actually "objects".  







Absolutely everything in PS is treated as an object, even a simple string of characters, e.g.:

<pre>
PS C:\> "hello world"
hello world
PS C:\> "hello world" | Get-Member


   TypeName: System.String

Name             MemberType            Definition
----             ----------            ----------
Clone            Method                System.Object Clone(), System.Object ICloneable.Clone()
CompareTo        Method                int CompareTo(System.Object value), int CompareTo(string strB), 
.
.
.
</pre>

This will identify the string belong to object type "System.string".


You can have spaces in the name, but have to use curly brackets:

<pre>PS C:\> ${my variable} = "test123"
PS C:\> ${my variable}
test123
</pre>

$phrase = 'The variable is equal to $var'	#single quotes will treat contents literally
$phrase = "The variable is equal to $var" 	#double quotes will evaluate $var first before variable assignment. 

"`$var - The variable is equal to $var"   # The back-tick forces the "$" character to be treated as a literal character.
										 
In Linux "`n" has a special meaning, it means "new line",

PS C:\> "This is the first line, `nand this is the second line."
This is the first line, 
and this is the second line.

For more info on this and other special characters, see:

help about_Escape_Characters

 
You can return the number of items in an array by invoking the "count" method: 

$array.count	# Not sure where this info is documented because it isn't in the gm. 
3 
 
Similarly you can use other methods on a variable, e.g.:

 
$array[1].ToUpper() 	# to capitalize
ITEM2
$array[1].length		# to return character length 
5
$array[1].replace('2','50')
 
 
You can overwrite items inside an array:

PS C:\> $array
item1
item2
item3
PS C:\> $array[1] = $array[1].replace('2','50')

PS C:\> $array
item1
item50
item3
 
If you want to apply a method on a whole array, in PS v3, you do it like this
$array.toupper
ITEM1
ITEM50
ITEM3


Also when you do:

$array | gm
 
 
You will find that the array has a property called "length", so if we do:

$array | select-object -property length 
 
Note: this will give right-aligned data because the output is numeric. 

You can store whole tabular objects into a variable:

$services = Get-Service

When that is the case, then try:

$services | gm

You will find that the variable is of the same object type as "get-service". This means that "$services" has the same properties, methods,....and etc
as "get-service". As a result a shorthand notation can be used when dealing with object variables like this:

$services.name 				# only works in PSv3. for ps2, you have to do use for-each: $services | foreach-object {write-output $_.name}
 
The above prints out just the name property of each object, i.e. it is equivalent to: 

Get-Service |select-object -property name
 
or 

get-service | foreach-object {write-output $_.name}
 

You can also use a shorthand notation for applying methods to a wmi-object:

 
$objects = Get-WmiObject -Namespace "root\cimv2" -Class win32_service | Where-Object -FilterScript {$_.name -eq "bits"}
$objects.ChangeStartMode('disabled')  	#Where ChangeStartMode is a method.  
 

Note, quotes and square brackets (to specify array item) don't mix well. 

PS C:\> $name= "sher","mirfath","rayyan"
PS C:\> "Hello, my name is $name"
Hello, my name is sher mirfath rayyan	 # Here I want to mention Rayyan only

PS C:\> "Hello, my name is $name[2]"
Hello, my name is sher mirfath rayyan[2]	# This didn't work becuase quotes don't understand square brackets. 
											# The workaround for this is to make ps do a two-pass at the same command, this is done by 
											# introducing round brackets, and the contents of these are evaluated first:

PS C:\> "Hello, my name is $($name[2])"		# Note, $(...), is equivalent to linux's back ticks: `....` 
Hello, my name is rayyan

This $() used above is known as a sub expression. 

 
$name = read-host "type your name"		# This is the linux equivalent to:
										# echo "type your name"
										# read
$name									# echo $1
 
The above is useful for creating an interactive script. 

You can declare data type for a variable like this:

[int]$number = 10			# note, you can use gm to convert that this is a string. 
$number = $number * 100		# this does't output anything
$number
1000

Note: once you have assigned a variable with a data type, it will then always accept that type and will give error message if 
you data of another data type into this variable. 

 


There are a number of other data types that you can use:
[int]
[single] and [double]   - these are to do with floating numbers
[string]
[char]		- exactly one character, e.g. [char]$FavoriteLetter = 'T'
[xml][/xml]		- An xml document. This have be used to ensure that data fed into this data type is valid xml. 
[adsi]			- something to do with Active Directory Service Interface
 
Best practice: You should declare variable type when setting the variable for the first time.   
 
Here are some commands for managing variables:

New-variable	- You don't really need to use this 
set-variable	- You don't really need to use this 
remove-variable - Covered later
get-variable	- You don't really need to use this 
clear-variable  - You don't really need to use this 


You can delete a variable like this:

remove-variable number		# Note, you have to omit the dollar sign.