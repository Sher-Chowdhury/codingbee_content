---
ID: 92
post_title: 'PowerShell &#8211; Using object hierarchies for complex output'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-using-object-hierarchies-for-complex-output
published: true
post_date: 2014-05-11 00:00:00
---
Chapter 20 - Using object hierarchies for complex output

Previously we learned that a (collecion) object can contain a number of other (row) objects, and each row's field contains string values (i.e. a simple one-value object). However it is possible (and quite common) that these fields contain a whole object, rather than just a value. 

This means that you can have a situations where we have an object-within an object, which is embeded within another object, which is embeded within another object......and etc!


To look for these types of occurences, try:

Get-Process | Format-List -Property *       # This will list all properties for all processes. 

Scroll through the output and look for any property's value that is displayed within curly-brackets. In my case, I found one for a process called "jqs":

PS C:\> Get-Process -Name jqs | Format-List -Property *

__NounName                 : Process
Name                       : jqs
Handles                    : 188
VM                         : 38875136
WS                         : 1425408
PM                         : 2662400
NPM                        : 34928
Path                       :
Company                    :
.
.
.
.
StartInfo                  : System.Diagnostics.ProcessStartInfo
StartTime                  :
SynchronizingObject        :
Threads                    : {1344, 1452, 1460, 1472...}				# Here is what we are interested in. 
UserProcessorTime          :
.
.
.


Notice that the "threads" property's value is shown in curly brackets. This is powershell's way of saying that this is actually an object. 

Now if we di this:

PS C:\> Get-Process -Name jqs | Select-Object -Property threads
Threads
-------
{1344, 1452, 1460, 1472...}


You can see that the value is still appearing as indicator that this is an object. So if you want view this value in the form of an object, then you have to use the select-object's "-expandproperty" parameter instead:


PS C:\> Get-Process -Name jqs | Select-Object -expandProperty threads
BasePriority            : 4
CurrentPriority         : 10
Id                      : 1344
.
.
.

By default the info is displayed in list format, so we can show it in table format like this:

PS C:\> Get-Process -Name jqs | Select-Object -expandProperty threads | Format-Table

BasePriorit CurrentPrio          Id IdealProces PriorityBoo PriorityLev PrivilegedP StartAddres StartTime   ThreadState
          y        rity             sor         stEnabled   el          rocessorTim           s
                                                                        e
----------- -----------          -- ----------- ----------- ----------- ----------- ----------- ---------   -----------
          4          10        1344                                                  2088830773                    Wait
          4           5        1452                                                  2088830761                    Wait
          4           5        1460                                                  2088830761                    Wait
          4           4        1472                                                  2088830761                    Wait
          4           4        1608                                                  2088830761                    Wait

Although, this doesn't display the "waitreason" property since terminal screen isn't wide enough, so we do this:

PS C:\> Get-Process -Name jqs | Select-Object -expandProperty threads | Format-Table -Property basepriority,currentprior
ity,id,startaddress,threadstate,waitreason

       BasePriority     CurrentPriority                  Id        StartAddress         ThreadState          WaitReason
       ------------     ---------------                  --        ------------         -----------          ----------
                  4                  10                1344          2088830773                Wait           Executive
                  4                   5                1452          2088830761                Wait         UserRequest
                  4                   5                1460          2088830761                Wait         UserRequest
                  4                   4                1472          2088830761                Wait         UserRequest
                  4                   5                1608          2088830761                Wait         UserRequest

The expandproperty parameter is very useful, and you will find yourself using this quite often. 



You can also create nested-style objects like this as well (which we will cover a little later).

But before we do that, we will first look at a technique called "splatting". Splatting is all about passing parameters into a command in the form 
of a hash table. For example, lets say we have the following command:

PS C:\> Get-WmiObject -computername localhost -Class 'win32_OperatingSystem'

SystemDirectory : C:\Windows\system32
Organization    : Ordnance Survey
BuildNumber     : 7601
RegisteredUser  : ---
SerialNumber    : 00477-001-0000421-84442
Version         : 6.1.7601


Here we have passed 2 parameters into the get-wmiobject parameter, they are  "-computername" and "-win32_OperatingSystem". These 2 parameters can also be fed into the get-wmiobject parameter using a hash table, but first we have to create the hashtable:

$params = @{computername=$computername;
            class='win32_OperatingSystem'            
		}

Now, since this hash table's property names matches those available parameter names of get-wmiobject, it means that the hashtable is now ready to be fed into the get-wmiobject, hence another way to write:

PS C:\> Get-WmiObject -computername localhost -Class 'win32_OperatingSystem'

Is to write:

PS C:\> Get-WmiObject @params

Notice here that we preceded @ rather than $. In Ps, this is way to tell the command (get-wmiobject) to:

     "take whatever characters comes after it as a variable name, and read whatever 
	 is inside the variable as a hashtable, and that the keys are parameter names. 
	 Expand those out, and feed the values from the hashtable to those parameters". 
  
In this context, the "@" is referred to as a splat operator.   	

Here is more info about this: http://technet.microsoft.com/en-us/magazine/gg675931.aspx

Note: although logicial, the following will not work:
PS C:\Users\SChowdhury\Desktop> $computername='localhost'
PS C:\Users\SChowdhury\Desktop> $class='win32_OperatingSystem'
PS C:\Users\SChowdhury\Desktop> Get-WmiObject $computername $class		# Unfortunately this line will generate an error message. 


Now, lets take a look at creating our own nested object. Lets assume that you want to create a collection (object), that lists machine info about each machine. Hence this (collection) object contains row (objects), and each row gives the following info about each machine. The properties for this (collection) object table are:

	- computername  (this will be the parameter that we will pass into the script/function)
	- SPVersion 
	- OSversion 
	- HDD info			# This will become a nested object since each machine can contain 2 or more hdds. 
						# In particular we want the following info:
							# Drive's ID
							# disk size
							# free space


Here is the code that shows how to do the above (i avoided creating it in a function to make it easier to follow):

############################################################### Start script
[cmdletbinding()]	
param(
    [string[]]$computernames 
)

$maintable = @()

foreach ($computername in $computernames) {
   
    # $computername                  # this will be the first property of our main table, which will create later. 

    # first get the "OSversion" and SPVersion info.
    $params = @{
        computername=$computername;
        class='win32_OperatingSystem'            
    }
    
    $os = Get-WmiObject @params
    
        
    # $os.version                    # This will be used later when creating the main table.               
    # $os.ServicePackMajorVersion    # This will be used later when creating the main table.  

    # Now get the hdd info for the given machine so that we can create our hdd collection table. 
    $params = @{
        computername=$computername
        class="win32_logicaldisk"
        filter='drivetype=3'
    }  
    
    $HddInfo = Get-WmiObject @params	# This variable stores all the hdd info for a given machine. 
        
	# Now create the hdd object table

    $HddCollection = @()

    foreach ($Hdd in $HddInfo){

        $HddSubsetInfo = @{
            DiskID=$Hdd.DeviceID;
            DiskSpac=$Hdd.size;
            FreeSpace=$Hdd.freespace
        }
    
    $HddRow = New-Object -TypeName psobject -Property $HddSubsetInfo        # this converts the 2-column (key/value) associative table into an
																			# object, where the key names, becomes the column (property) names.
    $HddCollection += $HddRow
    
    }
    
    
    # Now create the main table which will contain all the info gathered above. 
    
    #$HddCollection

    
    $MaintableAssociativeArray = @{
        computername=$computername;
        OSVersion = $os.version;
        SPVersion = $os.ServicePackMajorVersion;
        HDDInfo   = $HddCollection					# here we are placing a whole (collection) object as a single field.   
    }
    $maintableRow = New-Object -type psobject -Property $MaintableAssoiativeArray

    $maintable += $maintableRow
    
}
$maintable 

############################################################### End script

Now when you run the script, you should get:

PS C:\Users\SChowdhury\Desktop> .\ch20-testfile.ps1 -computernames nd24228, localhost

HDDInfo                              computername                                                    SPVersion OSVersion                          
-------                              ------------                                                    --------- ---------                          
{@{FreeSpace=121001988096; DiskID... nd24228                                                                 3 5.1.2600                           
{@{FreeSpace=5575049216; DiskID=C... localhost                                                               1 6.1.7601                           

As expected, the curly bracket entries in hddinfo indicates that this is a nested object, so we can do this to look inside:


PS C:\Users\SChowdhury\Desktop> .\ch20-testfile.ps1 -computernames nd24228, localhost | Select-Object -ExpandProperty hddinfo -Property computername

                           FreeSpace DiskID                                                           DiskSpac computername                       
                           --------- ------                                                           -------- ------------                       
                        121001644032 C:                                                           160038907904 nd24228                            
                          5574819840 C:                                                            42842714112 localhost                          
                         40796897280 E:                                                            42946523136 localhost                          

						 
Note: I include computername property so that I can see which row relates to which higher level object. 

Another way to do this is by using "format-custom" command. 

PS C:\Users\SChowdhury\Desktop> .\ch20-testfile.ps1 -computernames nd24228, localhost | format-custom -property *

This command will actually expand the entire object and display it in an xml like fashion (aka a tree like fashion)

 
Another way to view the nested object is by using the "powershell's array syntax" like this:

PS C:\Users\SChowdhury\Desktop> $nestedobject = .\ch20-testfile.ps1 -computernames nd24228, localhost
PS C:\Users\SChowdhury\Desktop> $nestedobject

HDDInfo                              computername                                                    SPVersion OSVersion                          
-------                              ------------                                                    --------- ---------                          
{@{FreeSpace=120997146624; DiskID... nd24228                                                                 3 5.1.2600                           
{@{FreeSpace=5574144000; DiskID=C... localhost                                                               1 6.1.7601                           

PS C:\Users\SChowdhury\Desktop> $nestedobject.hddinfo

                                       FreeSpace DiskID                                                                                   DiskSpac
                                       --------- ------                                                                                   --------
                                    120997146624 C:                                                                                   160038907904
                                      5574144000 C:                                                                                    42842714112
                                     40796897280 E:                                                                                    42946523136
 
You can then access, a particular row like this:


PS C:\Users\SChowdhury\Desktop> $nestedobject.hddinfo[1]

                                       FreeSpace DiskID                                                                                   DiskSpac
                                       --------- ------                                                                                   --------
                                      5574144000 C:                                                                                    42842714112

And even do this to access a nested object's property:

PS C:\Users\SChowdhury\Desktop> $nestedobject.hddinfo[1].FreeSpace
5574144000

Another way to access nested object is using the foreach loop (along with the powershell's array syntax), e.g:

foreach ($each_row_in_nested_object  in   $collection.Property.That.Houses.Nested.Object){
	"Here is an object:"
	"$each_row_in_nested_object"
}


To summarise we looked at 4 ways to access nested objects:

	- using select-object, and it's "-expandproperty" parameter
	- using the "format-custom" command
	- Using powershell's array syntax  
	- using foreach loop