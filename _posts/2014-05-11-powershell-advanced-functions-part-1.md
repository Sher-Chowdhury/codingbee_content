---
ID: 81
post_title: 'PowerShell &#8211; Advanced Functions (part 1)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-advanced-functions-part-1
published: true
post_date: 2014-05-11 00:00:00
---
Chapter 6 - skipped, because it is common sense stuff.

Chapter 7 - Advanced functions, part 1

So far we have only looked at basic functions. Now we we will look at how create an advanced functions. Here is the high-level syntax of an advanced function:

function <name> {
	[CmdletBinding()]	# indicates that this is an advanced function.
	param(
						# input parameters go here. 	
	)

	BEGIN {} 
	PROCESS {} 
	END {}

}

You should try to name your functions in the consistent "verb-noun" format.  
 
get-verb	# This simply gives a complete list of microsoft's approved verbs.

BEGIN{} ,	PROCESS{},	and END {} are three named scriptblocks. These are similar to the awk command, and their header footer system. 

When the function is executed, ps runs the BEGIN block first, and the END block last. The PROCESS block:

	- is run once if function is triggered using just it's parameters
	- will run multiple times if function is called with a pipeline input, in which case it will run once for each object. 

If an array of data is fed through, e.g. say we have a computernames passed through as an array, then you can process them in the PROCESS block like this:

BEGIN {"Here is a list"} 
PROCESS {
    foreach ($computer in $computername) {
        "this computer is called: $computer"
    } 
}
END {'List has ended.'}



When using functions, one thing you will be doing a lot of is creating your own custom collection (table). This is done in a 3 step process (within a foreach statement):

	1. Gather each data sources and store them in variable objects.
	2. Define our custom object's new property names and assign values to them. This is done using hash tables. 
	3. Transform the hash-table into an object. 
	

Here is an example of this 3 step process:


Step 1
Lets say we want to create a 3 column (property) collection (note this colleciton will only house one row (object)):

	1. The "version" property from the win32_operatingsystem class
	2. The "servicepackmajorversion" property again from the win32_operatingsystem class. 
	3. The "Serialnumber" property from the win32_BIOS class 
	4. The "model" property from the win32_computersystem class again
	5. The "manufacturer" property from the win32_computersystem class

Step one: Gather the data sources
	
$OperatingSystemInfo = Get-WmiObject -Class win32_operatingsystem   # This object variable will contain values for the "version" and 
																	# "servicepackmajorversion" properties)

$biosInfo = Get-WmiObject -Class win32_bios							# This object variable will contain the value for the "Serialnumber" property.
																	# Since we plan use only value from this class, then we can be just lazy and do:
																	# (Get-WmiObject -Class win32_bios).serialnumber

$computerInfo = Get-WmiObject -Class win32_computersystem			# This object variable will contain values for the "model" and "manufacturer"
																	# properties



Step 2: Now that we have our datasources, we now define the property names for our custom object and assign values to them. This is done using hash tables:

$properties = @{
	'OSVersion'=$OperatingSystemInfo.version;						# Note, i dont think the single quotes are needed here,
	'SPVersion'=$OperatingSystemInfo.servicepackmajorversion;
	'BIOSSerial'=$biosInfo.serialnumber;
	'Manufacturer'=$computerInfo.manufacturer;
	'Model'=$computerInfo.model
}


Note: As indicated above, the general structure of a hashtable is as follows:

$properties = @{
	property's-name=property's-value;
	property's-name=property's-value;
	property's-name=property's-value;
	property's-name=property's-value;
				.
				.
				.
	property's-name=property's-value			# Note: the last line doesn't end with a semi-colon.
}
 

This will create the $properties object variable. The object-type of this variable is "hashtable". If you do gm:

$properties | gm

Then you will find that the it's properties are actually generic hashtable related properties, and doesn't correspond to the properties that have been defined in the above hash table, however the following does still work:

PS C:\> $properties.OSVersion
5.1.2600

PS C:\> $properties.SPVersion
3

PS C:\> $properties.BIOSSerial
HUB7350W1M

PS C:\> $properties.Manufacturer
Hewlett-Packard

PS C:\> $properties.Model
HP Compaq dc7700p Small Form Factor


Next if you want convert your hash-table (which is a bit like a pseudo object), into a proper object, then you need to do step 3.



Step 3 

Transforming a hash table into a real object is done using new-object:

$collection = new-object -typename PSObject -property $properties			#in this instance, this collection only contains one object (row).  

Note: A hashtable (aka associative array) is essentially a two-column table, first column is the "key" and second column is the "value". The above command essentially rearranges this so that each key's name becomes a column name, and each value falles under the corresponding value. 

Here, the "PSObject" typename is specifically designed for this purpose, i.e. convert a hash-table into an object. The resulting object is of the 
object type "PSCustomObject", which is essentially a generic object. 

Also note that "-property" is spefically designed to only accept a hashtable. See help page for new-object, to confirm this. 

Now if you output both the hashtable and custom object:

PS C:\> $properties
Name                           Value
----                           -----
Manufacturer                   Hewlett-Packard 
OSVersion                      5.1.2600       
BIOSSerial                     HUB7350W1M     
Model                          HP Compaq dc7700p Small Form Factor
SPVersion                      3 

PS C:\> $collection
Manufacturer : Hewlett-Packard
OSVersion    : 5.1.2600
BIOSSerial   : HUB7350W1M
Model        : HP Compaq dc7700p Small Form Factor
SPVersion    : 3

You will find both output the same information, but in slightly different format, however if you do:

$collection | gm

Then you will find that this object's properties now correspond with what has defined in the hash-table!!! 




So far we have seen how to create an (collection) object that only contains one (row) object. 

Now if you want to create a proper collection object that houses a group of objects, then you can do it like this:

$bigcollection = @()				# This creates an empty array. At this stage this variable doesn't belong to a typename, which means that 
									# you cannot do "$bigcollection | gm" yet.
									# the syntax for creating an empty array isn't very intuitive, but that's how it is, as described here:
									# http://technet.microsoft.com/en-us/library/ee692797.aspx (see the empty array section)



$bigcollection += $collection		# This essentially adds/appends our custom object to the array. $bigcollection will now take on the typename
									# of the $collectiontype.
									
If you want to delete this collection object, then simply do:

Remove-Variable -Name bigcollection				# Don't forget to omit the "$" symbol.