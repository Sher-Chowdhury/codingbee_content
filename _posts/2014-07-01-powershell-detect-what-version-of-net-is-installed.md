---
ID: 124
post_title: 'PowerShell &#8211; Detect what version of .NET is installed'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-detect-what-version-of-net-is-installed
published: true
post_date: 2014-07-01 00:00:00
---
http://stackoverflow.com/questions/199080/how-to-detect-what-net-framework-versions-and-service-packs-are-installed

&nbsp;

The tale given in the first answer in the above stackoverflow questions shows what to look for. Then do:

&nbsp;

[powershell]
cd HKLM:\SOFTWARE\Microsoft\NET Framework Setup\NDP
ls
[/powershell]

This should give output that looks like:

<pre>
cd HKLM:\SOFTWARE\Microsoft\NET Framework Setup\NDP
ls

SKC  VC Name                           Property
---  -- ----                           --------
  1   0 CDF                            {}
 23   6 v2.0.50727                     {Install, Version, Increment, SP...}
  2   5 v3.0                           {Version, CBS, Increment, Install...}
  1   5 v3.5                           {Version, CBS, Install, InstallPath...}
  2   0 v4                             {}
  1   1 v4.0                           {(default)}
</pre>

now let's take a look at .NET 3.5 in particular:
<pre>
PS C:\> get-childitem -path "HKLM:\SOFTWARE\Microsoft\NET Framework Setup\NDP" | Where-Object -FilterScript {$_.name -match "v3.5"}


    Hive: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP


SKC  VC Name                           Property
---  -- ----                           --------
  1   5 v3.5                           {Version, CBS, Install, InstallPath...}
</pre>

Now if we drill down the property field we get:

<pre>
PS C:\> (get-childitem -path "HKLM:\SOFTWARE\Microsoft\NET Framework Setup\NDP" | Where-Object -FilterScript {$_.name -m
atch "v3.5"} ).property
Version
CBS
Install
InstallPath
SP
</pre>

The above actually outputs the field as strings, and not in a hashtable format that we are expecting. If we look at the same info via regedit, we see:

<a href="http://codingbee.net/wp-content/uploads/2014/07/regedit.png"><img src="http://codingbee.net/wp-content/uploads/2014/07/regedit.png" alt="regedit" width="864" height="468" class="alignnone size-full wp-image-1179" /></a>

Here, we want to view the highlighted parts, to view them we use the "get-itemproperty" cmdlet, like this:

<pre>
PS C:\> get-childitem -path "HKLM:\SOFTWARE\Microsoft\NET Framework Setup\NDP" | Where-Object -FilterScript {$_.name -ma
tch "v3.5"} | Get-ItemProperty


PSPath       : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v3.5
PSParentPath : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP
PSChildName  : v3.5
PSProvider   : Microsoft.PowerShell.Core\Registry
Version      : 3.5.30729.5420
CBS          : 1
Install      : 1
InstallPath  : C:\Windows\Microsoft.NET\Framework64\v3.5\
SP           : 1
</pre>

&nbsp;

&nbsp;