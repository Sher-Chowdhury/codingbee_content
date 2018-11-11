---
ID: 67
post_title: 'PowerShell &#8211; Windows Media Instrumentation (WMI)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-windows-media-instrumentation-wmi
published: true
post_date: 2014-05-11 00:00:00
---
WMI is a standalone component and Powershell only interacts with it.

WMI essentially houses a massive collection of system information and tries to organise this data in a meaningful way.

Here is very useful guide:

[powershell]
help about_WMI_Cmdlets
[/powershell]

At the top level, WMI is organised into namespaces (which are a bit like folders that ties to specific product or
technology.). Here are some example namespaces:

<strong>namespace name:</strong> root\CIMv2
<strong> Description:</strong> This houses all OS and Hardware info.

<strong>namespace name:</strong> root\MicrosoftDNS
<strong>Description:</strong> This houses info about the DNS server, assuming your machine is running as a dns server.

<strong>namespace name:</strong> root\securitycenter
<strong>Description:</strong> This houses info about firewalls, antivirus, and antispyware utilities.

Within each namespace, WMI is divided into a series of classes, a class is something that WMI knows how to query. Here are a few example classes, along with the namespace they reside in:

<strong>class name:</strong> AntiVirus-Product
<strong>located within the namespace:</strong> root\securitycenter
<strong> description:</strong> This class is designed to hold information about anti-spyware products

<strong>Class name:</strong> Win32_LogicalDisk
L<strong>ocated within the namespace:</strong> root\CIMv2
<strong> Description:</strong> This class is designed to hold information about logical disks

Note: There can be classes can exist, but doesn't hold any infomation, e.g. maybe the floppy disk class.

A class is essentially a text based equivalent of a config-window. The settings (content) of each class can be modified
using a command called "invoke-wmimethod". This command will be covered later.

Now going back to namespaces, to view all the available namespaces, do:

[powershell]
Get-WmiObject -Namespace root -Class __Namespace
[/powershell]

Note, this might give the output in list mode, in which case use select-object to get rid of some properties to make it all fit.

Note, the properties starting with 2 underscores, are referred to as "system properties".

If you want to view, a list of all the classes for a given namespace, then do:

[powershell]
Get-WmiObject -Namespace root\{namespace-name} -List
[/powershell]

e.g.:

[powershell]
Get-WmiObject -Namespace root\CIMV2 -List
[/powershell]

And if you just want to view the win32_logicaldisk class which resides in CIMV2, then do:

[powershell]
Get-WmiObject -Namespace root\CIMV2 -List | Where-Object -FilterScript {$_.name -eq &quot;win32_logicaldisk&quot;}
[/powershell]

If you want to view more info about this class, then do:

[powershell]
Get-WmiObject -Namespace root\CIMv2 -class {class-name}
[/powershell]

Note: You should always declare namespace because class names are not always unique across namespaces.

In most cases, there is class names are unique throughout WMI. For example, all computers have one BIOS, hence there is

always only one instance of the win32_bios class:

[powershell]
Get-WmiObject -class win32_bios
[/powershell]

However in some cases there can be more than instances classes with the same name. For example a machine can have many services running (win32_services):

[powershell]
Get-WmiObject -class win32_service
[/powershell]


Note: You can uniquely refer to each instance by their "__path" property's value, which are unique.

You can control a class, by using a "method". However Microsoft are trying to move away from methods, and
instead are developing custom commands for interacting with classes instead. These commands interacts
with WMI internally, so that you won't have to.

If you are looking for a particular class, then it can be time consuming to find it if you don't know which namespace it is in.
That's why, a good way to browse through classes is using a third party GUI software called Sapien Windows Explorer,
which is made by sapien.

In PS, there are 2 groups of commands that can be used to interact with WMI:

1. wim cmdlets - e.g. get-wmiobject and invoke-wmimethod. These are legacy commands which slowly be phased out.
- They communicate over Remote Procedure Calls (RPC) instead of WS-MAN
- It can take awhile to get these commands to work through a firewall.

2. cim based commands - These are the more up to date commands that microsoft are investing money in.
- They are only available in PS v3
- They work over ws-man
- There are a lot more of these command available.

The "get-CIMinstance" is the equivalent version of "get-wmiobject".

Note: Unfortunately there is no documentation for what individual namespaces and classes do. Microsoft themselves have not written any information on them. So you are stuck with using google for finding info.

For more info, checkout the book "powershell and wmi" by Richard Siddaway (2012)