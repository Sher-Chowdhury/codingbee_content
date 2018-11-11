---
ID: 110
post_title: 'PowerShell &#8211; Environment Variables'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-environment-variables
published: true
post_date: 2014-06-05 00:00:00
---
to set an environment variable, you can do this

[powershell]

[environment]::SetEnvironmentVariable(&quot;TestVar&quot;, &quot;a simple string&quot;, &quot;Machine&quot;)

[/powershell]

Note, I think you can set the value at sessions/user/machine level. In the above case I have set them to the machine level.

Now to retrieve an environmenment from somewhare else, e.g. from another script, do this:

[powershell]

$TestVar = [System.Environment]::GetEnvironmentVariable(&quot;TestVar&quot;,&quot;Machine&quot;)
&quot;this variable contains: $TestVar&quot;

[/powershell]

You can only store strings as environment variables. That means that you can't store object-variables as environment variables.

However you can output an object-variable to an xml file, and then import it back to an object variable again using import-Clixml and export-Clixml. With this approach all you need to is keep track of where you stored the outputted xml file, so that you can import as and when need.

A common use for this is to store your "pscredential" object

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

useful links:

http://technet.microsoft.com/en-us/library/ff730964.aspx (ready the part about "GetEnvironmentVariable")

&nbsp;

http://www.thomas-franke.net/powershell-manage-passwords-credentials/