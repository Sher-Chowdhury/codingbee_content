---
ID: 89
post_title: 'PowerShell &#8211; Creating a Custom Type Extension'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-creating-a-custom-type-extension
published: true
post_date: 2014-05-11 00:00:00
---
Chapter 17 - Creating a Custom Type Extension
 
In an earlier chapter we learnt about how to change the way a function's output looks like. In this chapter we are going learn about how to add our own entries in our custom command's get-member profile. We can only add an entry which belongs to the type-extension family. 
 
When you do:

get-process | gm

YOu will find entries that are properties and methods. However you will also find entries that are of the following type:

	- AliasProperties
	- NoteProperties
	- ScriptProperties
	- ScriptMethods
	- PropertySets
	
These are called "type extensions".

These extensions are added onto each object by a system called the "Powershell's Extensible Type System" (ETS). Why are these extensions added? Each of these extensions have a different purpose:

AliasProperty - This is basically an alias for a given property. 
 
PS C:\> Get-Process | Select-Object -Property handlecount,handles	# This will output two identical columns, since "handles" is an AliasProperty of 
																	# "handlecount". The only difference being the header title. 
 
 
Get-Process | Select-Object -Property processname,name		# Here we get 2 identical columns again, since "name" is an AliasProperty 
															# for "processname" 
 
 
NoteProperty - This is a place where powershell may store internal data about an object.  
 
 
ScriptProperty - This is powershell script that is run to output the script property. This doesn't usually access an external resource, instead it just reformats/reveals  something that is already part of the object. e.g. you could use it to dispaly disk space from mb, to gb.


ScriptMethod - This is the same as ScriptProperty, although it may only take action but not output anything. 


PropertySet - This is a pre-defined collection of properties that has been grouped together. For example:

PS C:\> Get-Process | Select-Object -Property PSResources | Format-Table     # "PSResources" is actually a propertyset. 

We can create custom extensions for our modules in the same style that we did to create the custom view file. I.e. we create an xml file. However, an important thing to remember is that this xml file must have the following extension:

FileName.ps1xml		# Note, it cannot end with "format.ps1xml", because that ending is reserved for custom views. 

All the built-in extension types that ps uses are stored in the following xml file (which is called "types.ps1xml"):

$pshome\types.ps1xml


The general format of this xml file is as follows:

<?xml version="1.0" encoding="utf-8" ?>
<Types>
    
	<Type>											# "Type" will house the info for a single typename. 
        <Name>HMRCtoolcustomformat</Name>					# Here you specify the "Typename" value (which appears on first line of gm).
        <Members>									# "Members" will house the info for one or more type-extensions 	
            <AliasProperty>					# Here we define the first type-extension, which in this case is an alias property. 
                <Name>Name</Name>				# This is the name of this type-extension.
                <ReferencedMemberName>ProcessName</ReferencedMemberName>
            </AliasProperty>
            <AliasProperty>					# Here is the next type-extension, this agian is an alias property
                <Name>Handles</Name>
                <ReferencedMemberName>Handlecount</ReferencedMemberName>
            </AliasProperty>
            <ScriptProperty>				# Here is the third type-extension, this time it is a script-property
                <Name>Path</Name>
                <GetScriptBlock>$this.Mainmodule.FileName</GetScriptBlock>
            </ScriptProperty>
        </Members>
    </Type>
	
	<Type>
       .
	   .
    </Type>
	
	<Type>
       .
	   .
	   .
    </Type>

</Types>

 
Now in my module, created the following extension-type file:

PS C:\Users\SChowdhury\Documents\WindowsPowerShell\Modules\hmrctool> ls

Mode                LastWriteTime     Length Name
----                -------------     ------ ----
-a---        02/09/2013     14:47        376 hmrcextensiontype.ps1xml     	# This is the new file created.
-a---        27/08/2013     17:26       1653 hmrctool.format.ps1xml
-a---        28/08/2013     09:31       5236 hmrctool.psd1
-a---        02/09/2013     14:19        749 hmrctool.psm1
 
 
"hmrcextensiontype.ps1xml" has the following content:

<?xml version="1.0" encoding="utf-8" ?>
<Types>    
	<Type>											 
        <Name>HMRCtoolcustomformat</Name>			
        <Members>									 	
            <ScriptProperty>				
                <Name>GetRidofBinApartment2</Name>					#This will replace all (column 2) instances of letter "b" with the letter "O".
                <GetScriptBlock>$this.column2 -replace 'b','O'</GetScriptBlock>
            </ScriptProperty>
            <ScriptMethod>							# See note below. 
                <Name>CanPing</Name>
                <Script>
                    Test-connection -computername $this.column2 -quiet
                </Script>
            </ScriptMethod>
        </Members>
    </Type>
</Types> 



https://groups.google.com/forum/#!topic/microsoft.public.windows.powershell/r_oUArRP6_8 - explains the difference of $_ and $this
Here is another link:
http://powershell.com/cs/forums/t/11373.aspx 

Detour: Also you can add new type-extension a type-extension straight from the command line, using "add-member":

$object | add-member -MemberType noteproperty `
					 -Name exampleproperty `
					 -Value "hello!"				# Note, i haven't tried this. 


Also checkout:

help about_Automatic_Variables		# This gives info about things like $$, $?



Note: The above script-method will do a ping test for all items in column b....which is handy if column b is a list of computer names. Note, "Test-connection" is the ps equivalent of the ping command. Hence use this command to test if can connect to another machine. 


Once the xml file is ready, you can then load it into memory. For custom views, we did this using "Update-FormatData", however for type-extension, we use the following command instead:

Update-TypeData -PrependPath {filename.ps1xml}

After that you can check if it has worked by running the custom command into gm

PS C:\> new-hmrcobject -computername machine | gm

   TypeName: HMRCtoolcustomformat

Name                  MemberType     Definition                                                               
----                  ----------     ----------                                                               
Equals                Method         bool Equals(System.Object obj)                                           
GetHashCode           Method         int GetHashCode()                                                        
GetType               Method         type GetType()                                                           
ToString              Method         string ToString()                                                        
column1               NoteProperty   System.String column1=machine                                            
column2               NoteProperty   System.String column2=bbb                                                
column3               NoteProperty   System.String column3=ccc                                                
CanPing               ScriptMethod   System.Object CanPing(); 						# This is the new script method.
GetRidofBinApartment2 ScriptProperty System.Object GetRidofBinApartment2 {get=$this.column2 -replace 'b','O';}	# This is the new script property


And, the standard output is:

PS C:\Users\SChowdhury\Documents\WindowsPowerShell\Modules\hmrctool> new-hmrcobject -computername test

columnX                                                      columnY         columnZ
-------                                                      -------         -------
test                                                         bbb             ccc
test                                                         bbb             ccc
test                                                         bbb             ccc

Lets now see if our new script-property works:

PS C:\> new-hmrcobject -computername test | Select-Object -Property column1,column2,column3,GetRidofBinApartment2

column1                            column2                            column3                            GetRidofBinApartment2
-------                            -------                            -------                            ---------------------
test                               bbb                                ccc                                OOO
test                               bbb                                ccc                                OOO
test                               bbb                                ccc                                OOO

Note: this affects each row object. 

Now lets try using the method. 

$tempObj = new-hmrcobject -computername machine			# first store the object in a variable. 

PS C:\Users\SChowdhury\Documents\WindowsPowerShell\Modules\hmrctool> $tempObj.Canping()			# Now apply the method to the (object) variable. 
False
False
False

The above shows "false" because we fed through fake computer names. 

 
So far, we manually loded the type-extension xml file into memory, the better option would be for this to happen automatically when importing the module, and to do this, we use the New-ModuleManifest command again:


New-ModuleManifest -Path .\hrmctool.psd1 `		
-Author 'Sher' `
-CompanyName 'HMRC' `
-Copyright '(c) Sher Chowdhury' `
-Description 'this is a test description' `
-FormatsToProcess .\hmrctool.format.ps1xml `		
-ModuleVersion 1.0 `
-PowerShellVersion 3.0 `
-RootModule .\hmrctool.psm1	`						 
-TypesToProcess '.\hmrcextensiontype.ps1xml'		# Here is the new line for this. 

Note, if you already have a manifest (psd1) file, then you can simply modify this file instead.