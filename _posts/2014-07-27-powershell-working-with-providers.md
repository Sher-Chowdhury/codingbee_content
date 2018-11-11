---
ID: 59
post_title: 'PowerShell &#8211; Working with providers'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-working-with-providers
published: true
post_date: 2014-07-27 00:00:00
---
Chapter 5 - Working with providers


Wordpress has things called plugins, which can extend wordpress's features. Powershell has similar features which are called snapins and modules.

In PS, we have things called "PSDrives". These are a bit like virtual hard drives (vhdds). Some of these virtual hdds stores a normal files system, that contains files and folders, whereas others are specialized "storage containers", which can store all kinds of things, that are not of the traditional files/folders structure. So in other words we have different types of psdrives. These types have names called "psproviders". To view a list of all the various types of psproviders do:

<pre>
PS C:\> Get-PSProvider
Name            Capabilities                           Drives
----            ------------                           ------
Alias           ShouldProcess                          {Alias}
Environment     ShouldProcess                          {Env}
FileSystem      Filter, ShouldProcess, Credentials     {C, E, A, D}
Function        ShouldProcess                          {Function}
Registry        ShouldProcess, Transactions            {HKLM, HKCU}
Variable        ShouldProcess                          {Variable}
Certificate     ShouldProcess                          {Cert}
WSMan           Credentials                            {WSMan}
</pre>

Here we have several types of psdrives, alias, environment, filesystem.....and etc. 

As you can see, 4 of our psdrives (C, E, A, D) are of the psprovider type of "filesystem". These are like the traditional hdds. 



So PSProviders are essentially storage containers, where each of these containers behave like virtual hdds, but only a subset do actually have a hdd like folder-tree/file appearance. 

But remember, all psdrives, (regardless of what psprovider type they are) are essentially) hdds. For that reason, it means you can cd into them, e.g.:

<pre>
PS C:\> cd Env:\
PS Env:\>
</pre>

Like wise, for the same reason, you can also do "ls":

<pre>
PS Env:\> ls

Name                           Value
----                           -----
ACPath                         C:\Program Files (x86)\Lenovo\Access Connections\
ALLUSERSPROFILE                C:\ProgramData
APPDATA                        C:\Users\schowdhury\AppData\Roaming
CommonProgramFiles             C:\Program Files\Common Files
CommonProgramFiles(x86)        C:\Program Files (x86)\Common Files
CommonProgramW6432             C:\Program Files\Common Files
COMPUTERNAME                   ND26460
ComSpec                        C:\WINDOWS\system32\cmd.exe
deployment.expiration.check... false
FP_NO_HOST_CHECK               NO
HOMEDRIVE                      C:
HOMEPATH                       \Users\schowdhury
LOCALAPPDATA                   C:\Users\schowdhury\AppData\Local
LOGONSERVER                    \\OSDC01
.                                                                                         
.
....etc.
</pre>



Since the "env" psdrive is not a typical hdd, it means that it shows us whatever is inside it. As it turns out, this drives contains an associative array. In powershell, there is a quick-hand way to access a key's value (from a psdrive that has an associative arrays stored in it):

<pre>PS C:\> $env:COMPUTERNAME	
codingbee
</pre>


<pre>PS C:\> Get-PSDrive
Name           Used (GB)     Free (GB) Provider      Root                                               CurrentLocation
----           ---------     --------- --------      ----                                               ---------------
A                                      FileSystem    A:\
Alias                                  Alias
C                  34.68          5.22 FileSystem    C:\
Cert                                   Certificate   \
D                                      FileSystem    D:\
E                   2.00         38.00 FileSystem    E:\
Env                                    Environment
Function                               Function
HKCU                                   Registry      HKEY_CURRENT_USER
HKLM                                   Registry      HKEY_LOCAL_MACHINE
Variable                               Variable
WSMan                                  WSMan
</pre>

We previously mentioned that a psprovider is a way to describe what type a psdrive is. Actually it is much more than that. A psprovider acts as a connector between the vhdd and the machine. This means that if the "alias" psprovider connector is used....then that vhdd becomes a psdrive of the "alias" psprovider type. This means that the psprovider that is used to connect the machine to the psdrive, essentially defines the type of that psdrive. Before a psdrive is connected, you can think of them as generic empty shell, that providers use to create psdrives. 

This means that the "get-psprovider" actually shows the list of available psproviders (connectors).....whereas "get-psdrive" shows all the available vhdds that have already been connected, along with the (ps)provider used to connect them.  

An analogy of a psprovider is essentially a bunch of cables, e.g. ethernet,hdmi,usb,scart....etc. 

Now you can make new psproviders (e.g. sata) available to powershell. This is usually done in one of the following ways:

	- installing a module  (covered later)
	- installing a snap-in  (covered later)
	- enabling a feature

Going back to the output of get-provider:
	
PS C:\> Get-PSProvider
Name                 Capabilities                                      Drives
----                 ------------                                      ------
Alias                ShouldProcess                                     {Alias}
Environment          ShouldProcess                                     {Env}
FileSystem           Filter, ShouldProcess, Credentials                {C, E, A, D}
Function             ShouldProcess                                     {Function}
Registry             ShouldProcess, Transactions                       {HKLM, HKCU}
Variable             ShouldProcess                                     {Variable}
Certificate          ShouldProcess                                     {Cert}
WSMan                Credentials                                       {WSMan}	
	
Here is a quick quide on what each of the capabilities means:

	shouldProcess - means that the provider supports the -whatif/-confirm parameters. 
	filter        - means that the provider supports the -filter parameter.
	credentials   - means that the provider lets you specify alternate credentials when connecting to data stores. There's the "-credential"
					parameter for this. 
	Transactions  - Means that the provider supports the use of transactions, which allows you to use the provider to make several changes,
					and then either rolls back or commit those changes as a single unit. 
	
	
To see and manipulate the data in each psdrive, you use commands that contains the word "item" in the second part of the name (this actually is a rule of thumb). To list these commands simply do:

PS C:\> get-command -noun *item*

However each psprovider's drives are essentially different tech, so each have their own quarks and while some commands work for one psprovider's psdrives, that might not be the case for another psprovider's psdrive. 

In powershell, an "item" is a generic term that refer to a folder or file.

New-Item testfile.txt -ItemType file 			#this creates a new file.
New-Item testfolder -ItemType directory 		#this creates a file.

new-item is effectively the "touch" and "mkdir" command combined into one. Files and folders are not allowed to have special characters  in their name (e.g. * and ?).  


However other items (e.g. registry items) are allowed to have wild cards in their name (and we set them using the "Set-ItemProperty" command - which is covered later). as a result, we have 2 parameter options for the "set-itemproperty" command:

-path 			# this allows wildcards
-literalpath 	# this doesn't allow parameters

now to alter a property of a registry, you use the following command:

<pre>Set-ItemProperty -Path {path-to-item} -PSProperty {name of item's property} -Value {new value for the property}
</pre>
Software vendors takes advantage of the concept of providers to allow powershell to change their software's settings. Providers can be used a bit like an API.