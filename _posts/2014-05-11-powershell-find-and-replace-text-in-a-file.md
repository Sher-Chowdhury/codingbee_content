---
ID: 100
post_title: 'PowerShell &#8211;  find and replace text in a file'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-find-and-replace-text-in-a-file
published: true
post_date: 2014-05-11 00:00:00
---
Special Chapter - find and replace text in a file

Here is an example:


$filescontent = Get-Content c:\path\to\file

$filescontent -replace "foo", "bar" | Set-Content c:\path\to\file

If you don't want to overwrite, then you can do:

copy-item -path "c:\path\to\file" -destination "c:\path\to\orig-file"

$filescontent -replace "foo", "bar" | Set-Content c:\path\to\file