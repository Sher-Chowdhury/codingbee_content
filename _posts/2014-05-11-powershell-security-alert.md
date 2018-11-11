---
ID: 70
post_title: 'PowerShell &#8211; Security Alert'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-security-alert
published: true
post_date: 2014-05-11 00:00:00
---
Chapter 17 - Security Alert

By default, as a security feature, when you double click on a powershell script, it will not run the script, 
it will instead open the script up in editor mode.

Also by default you cant run the script in ps by just entering the name, you have to always specify the full/relative path.  

There is a setting called ExecutionPolicy which controls whether a machine is allowed to run a ps scripts. You can 
view this here:

Get-ExecutionPolicy

By default this is set to "restricted" which means it won't let you run any ps script. You can change this setting
like this: 

Set-ExecutionPolicy -Executionpolicy {setting}
 
For more info, see:

help Set-ExecutionPolicy -full

If you want to run PS scripts, then microsoft recommends setting the executionpolicy to "RemoteSigned"