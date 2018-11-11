---
ID: 160
post_title: 'Powershell &#8211; Make a permanent change to the &#8220;path&#8221; environment variable'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-make-a-permanent-change-to-the-path-environment-variable
published: true
post_date: 2014-10-18 00:00:00
---
A common task you often need to do is append a new path to the path environment variable:

$ENV:PATH


The first way is simply to do:


$ENV:PATH="$ENV:PATH;c:\path\to\folder"

But this change isn't permenantly, $env:path will default back to what it was before as soon as you close your powershell terminal and reopen it again. That's because you have applied the change at the session level and not at the source level (which is the registry level). To view the global value of $env:path, do:

Get-ItemProperty -Path 'Registry::HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Session Manager\Environment' -Name PATH 

or, more specifically:

(Get-ItemProperty -Path 'Registry::HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Session Manager\Environment' -Name PATH).path

Now to change this, first we capture the original path that needs to be modified:

$oldpath = (Get-ItemProperty -Path 'Registry::HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Session Manager\Environment' -Name PATH).path

Now we define what the new path should look like, in this case we are appending a new folder:
$newpath = "$oldpath;c:\path\to\folder"

Note: Be sure that the $newpath looks how you want it to look, if not then you could damage your OS. 

Now apply the new value:

Set-ItemProperty -Path 'Registry::HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Session Manager\Environment' -Name PATH -Value $newPath

Now do one final check that it looks how you expect it:

Get-ItemProperty -Path 'Registry::HKEY_LOCAL_MACHINE\System\CurrentControlSet\Contro
l\Session Manager\Environment' -Name PATH).Path

You can now restart your powershell terminal (or even reboot machine) and see that it doesn't rollback to it's old value again. Note the ordering of the paths may change so that it's in alphabetical order, so make sure you check the whole line, to make it easier, you can split the output into rows by using the semi-colon as a delimeter:

($env:path).split(";")





http://blogs.technet.com/b/heyscriptingguy/archive/2011/07/23/use-powershell-to-modify-your-environmental-path.aspx