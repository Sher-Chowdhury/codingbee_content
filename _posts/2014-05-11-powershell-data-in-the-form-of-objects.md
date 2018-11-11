---
ID: 62
post_title: 'PowerShell &#8211; Data in the form of &#8220;objects&#8221;'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-data-in-the-form-of-objects
published: true
post_date: 2014-05-11 00:00:00
---
In PowerShell, there are lots of commands that gives tabular data, e.g.

<pre>get-process</pre>

In PS, we have some terminologies that describes different parts of these tables:

<ul>
	<li><strong>collection</strong>: This refers to the whole table.</li>
	<li><strong>object</strong>: single row in the table</li>
	<li><strong>property</strong>: column in the table</li>
	<li><strong>method</strong>: action that you can take on a given row (object)</li>
</ul>

Now to see what methods you can apply to each object in get-process, we do:



<pre>
PS C:\> Get-Service | get-member

   TypeName: System.ServiceProcess.ServiceController

Name                      MemberType    Definition
----                      ----------    ----------
Name                      AliasProperty Name = ServiceName
RequiredServices          AliasProperty RequiredServices = ServicesDependedOn
Disposed                  Event         System.EventHandler Disposed(System.Object, System.EventArgs)
Close                     Method        void Close()
Continue                  Method        void Continue()
CreateObjRef              Method        System.Runtime.Remoting.ObjRef CreateObjRef(type requestedType)
Dispose                   Method        void Dispose(), void IDisposable.Dispose()
GetType                   Method        type GetType()
.
...etc.
</pre>

On our machine, one of the get-service collection's object is called "browser":

<strong>
PS C:\> Get-Service -Name browser

Status   Name               DisplayName
------   ----               -----------
Running  browser            Computer Browser
</strong>


Let's say we wan to apply the "GetType" method on this object, then we do this using the following syntax:

<pre>
PS C:\> (Get-Service -Name browser).GetType()

IsPublic IsSerial Name                                     BaseType
-------- -------- ----                                     --------
True     False    ServiceController                        System.ComponentModel.Component
</pre>

This in turn can have it's own methods:

<pre>
PS C:\> (Get-Service -Name browser).GetType() | Get-Member

   TypeName: System.RuntimeType

Name                           MemberType Definition
----                           ---------- ----------
AsType                         Method     type AsType()
Clone                          Method     System.Object Clone(), System.Object ICloneable.Clone()
Equals                         Method     bool Equals(System.Object obj), bool Equals(type o), bool _MemberInfo.Equa...
FindInterfaces                 Method     type[] FindInterfaces(System.Reflection.TypeFilter filter, System.Object f...
</pre>



	
Note: here is another example of using methods, but this time for "get-date". 

<pre>
PS C:\> get-date

03 December 2015 13:37:59


PS C:\> (Get-date).ToShortTimeString()
13:38
PS C:\>
</pre>


Here is another very popular way of accessing a method:

<pre>
PS C:\> $date = Get-Date
PS C:\> $date

03 December 2015 13:38:52

PS C:\> $date.ToShortTimeString()
13:38
PS C:\>
</pre>

In Powershell, a "method" is simply a way of telling the object to do something, usually to itself. This concept, i.e. treating a table as a "collection" of "objects" means that you can work with data more easily (especially when compared to unix where you need to use grep, awk, and sed). In Powershell, you can extract data based on property names, and object's field entries. By default, the get-process
command only shows a subset of properties, because the screen isn't big enough to show all of them. However the command will show all the properties if you pipe it to a file. If you want to see all the available properties and methods for a collection, you need to use the get-member command. E.g. for get-process, you do:

<pre>get-process | get-member</pre>

Note, that sometimes you may need to use "-force" to access to the complete list, i.e.:

<pre>get-process | get-member -force</pre>

Note, everything about a collection, i.e. properties, object, are referred to as a member. Hence that'd where get-member gets it's name from. When you use get-member, you will discover a number of different types of properties:
<ul>
	<li>aliasproperty</li>
	<li>scriptproperty</li>
	<li>noteproperty</li>
	<li>property</li>
</ul>

	
You can ignore the various types and just think of them all as the same thing.

Now lets start by manipulating objects in a table. For get-process, if you want to re-arrange the rows based on the virtualmemory (vm) property, then do this:

<pre>get-process | sort-object -property VM</pre>

This sorts it by vm, followed by id:

<pre>get-process | sort-object -property VM,id</pre>

If you want to specify which property (columns) to output, then do:

<pre>get-process | select-object -Property ProductVersion,FileVersion</pre>

If you want to view the first 10 lines, you can do this using the select-object command:

<pre>
get-process | select-object -Property ProductVersion,FileVersion | select-object -first 10     
</pre>

Similarly for the last 10 lines you do:

<pre>
get-process | select-object -Property ProductVersion,FileVersion | select-object -last 10		</pre>


If you want to filter out rows, like we do in linux using grep, awk, and sed, then we need to use <strong>where-object</strong>. This will be covered later.

Note: When you look at the help files of any command that outputs a tabular output, you will find that they don't give a whole list of all available properties (columns), that's why you have to use the get-member command instead to get all this info.