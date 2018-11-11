---
ID: 69
post_title: 'PowerShell &#8211; Processing each row in a table (for-each)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-processing-each-row-in-a-table-for-each
published: true
post_date: 2014-05-11 00:00:00
---
Chapter 16  

some commands can run without giving any output at all, e.g. 

start-service {service name}
stop-service {service name}

However you can make them give a response using the -passthru option:

PS C:\Documents and Settings> start-service spooler -PassThru

Status   Name               DisplayName                           
------   ----               -----------                           
Running  spooler            Print Spooler                         

Note, for scripting you can output this to a file and check if it is successful by looking for a null file. 

Note: the "$?" exit code in linux works exactly the same way in PS too!!!!!!!!!!!!!!!!!!

For more about $? and others, check out:

help about_Automatic_Variables


In PS, there is a number of ways to do the equivalent of the for-each loop:

	1. Use a command collection's builtin for-each loop, e.g.:
			get-service | stop-service
		This is the simplest approach, but it isn't possible to take this approach all in all cases. 	
	2. This is the same technique as 1, but this time we will be changing a classes property using the 
	   WMI-invokemethod command (this feels like it shouldn't be bullet point here, and should have been covered 
	   earlier in this book). All the classes content (configuration) might not be configurable, but the ones 
	   that are, can be configured via the class's methods. To view all the available methods for a class, do:
	   
			get-wmiobject -class win32_networkadapterconfiguration | get-member
		
	   Once you found the method you want to change (e.g. anableDHCP), then use the invoke-wmimethod command:
	  
			get-wmiobject -class win32_networkadapterconfiguration | invoke-wmimethod -name enabledhcp
			
		The output can be a little confusing, however just look at the "returnvalue" output, which is a number, and 
		then google for more info about what that number means (since PS doesn't provide help info about classes).
		A return value of "0" means it is successful, anything else tends to indicate a problem.
		
	    Note, the CIM equivalent can be done using the the get-cimstance command and the invoke-cimmethod command. 
		
	3.  If it's not possible to use the above two methods, then you can use the generic "for-each" approach. For example,
	    if you do:
		
		  Get-WmiObject -class win32_service | gm
		  
		You will find that win32_service has a method called "change". You will notice that this method 
		actually lets you change multiple settings:
		
		Get-WmiObject -class win32_service | gm | Where-Object -FilterScript {$_.name -eq 'change'} | format-list
		
		Here, specify which setting within "change" that we want to modify, we use a special builtin variable called
		$null, this variable means "keep as is", hence if we want to change the "startpassword" (which is the 8 setting listed above), 
		then you first setup the syntax:
	    
			change($null, $null, $null, $null, $null, $null, $null, $null, "change-me")
		
		we don't have to specify any more nulls after the password, as ps will assume this. 
		
		Now we feed the above into the class (in this case only one instance called "bits"):
		
			Get-WmiObject -class win32_service | where-object {$_.name -eq 'bits'} | foreach-object -process {$_.change($null, $null, $null, $null, $null, $null, $null, $null, "change-me")}
	   
		The "-process" parameter is mandatory, and instructs the scriptblock to operate 
		within the for-eachobject container.
		
		Note: you can use foreach-object all the time instead of invoke-wmimethod, but people still use invoke-wmimethod
		because of habit. E.g to change the Changestartmode property of the "BITS" instance of the 
		win32_class, we do:
		
		Get-WmiObject -class win32_service | Where-Object {$_.name -eq 'bits'} | Invoke-WmiMethod -name changestartmode -ArgumentList "manual"
 
		Whereas, the foreach-object equivalent is:

		
		Get-WmiObject -class win32_service | Where-Object {$_.name -eq 'bits'} | ForEach-Object {$_.changestartmode("automatic")}
 
 
 So far we have covered 3 methods, now here is an example of doing a specific task, using each approaches. In this case
 the specific task is to stop all services that starts with "Bro*" in it's name:
 
Approach 1 - using batch cmdlets
get-service *Bro* | stop-service
 
Approach 2 - Using the foreach-object approach
get-service -name *Bro* | foreach-object -process {$_.stop}	# Here we used an object's "stop" method.
Note: not sure if you are supposed to include round brackets for the method, i.e. $_.stop()

Approach 3 - 
Get-WmiObject -class win32_service | where-object -filterscript {$_.name -like "*Bro*"} | invoke-wmimethod -name stopservice 
Note, get/start/stop-service cmdlets are "frontend" commnads for managing win32_service classes.  
 
Approach 2 and 3 combined
Get-WmiObject -class win32_service | where-object -filterscript {$_.name -like "*Bro*"} | foreach-object -process {$_.stopservice()}
Note, by convention, when using foreach-object, always include the 
closing brackets in the method's name, i.e. "stopservice()".

However, where possible, use the simplest way:
stop-service -name *Bro*