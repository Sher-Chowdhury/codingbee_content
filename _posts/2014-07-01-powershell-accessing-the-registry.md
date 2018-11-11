---
ID: 125
post_title: 'Powershell &#8211; Accessing the registry'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-accessing-the-registry
published: true
post_date: 2014-07-01 00:00:00
---
There are 2 main cmdlets you use to identify and navigate the registry (instead of using regedit). 

First of you navigate the registry in the same style as navigating a folder structure, i.e. you navigate it using cd and get-childitem. But the registry doesn't reside inside the c:\ drive, instead it resides in it's own drive called registry:


<pre>
PS C:\> Get-PSProvider

Name                 Capabilities                                      Drives
----                 ------------                                      ------
WSMan                Credentials                                       {WSMan}
Alias                ShouldProcess                                     {Alias}
Environment          ShouldProcess                                     {Env}
FileSystem           Filter, ShouldProcess                             {C, E, A, D}
Function             ShouldProcess                                     {Function}
Registry             ShouldProcess, Transactions                       {HKLM, HKCU}
Variable             ShouldProcess                                     {Variable}
Certificate          ShouldProcess                                     {cert}
</pre>

Here you should see that that all the registry info is stored in 2 drives, HKLM and HKCU. 

The other important cmdlet that you also need to use is the get-itemproperty cmdlet. Here's an example on how to <a href="http://codingbee.net/tutorials/powershell/powershell-detect-version-net-installed/" title="PowerShell - Detect what version of .NET is installed">explore the registry to find the version of .net installed</a>.