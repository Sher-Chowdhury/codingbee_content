---
ID: 83
post_title: 'PowerShell &#8211; Writing help'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-writing-help
published: true
post_date: 2014-05-11 00:00:00
---
Chapter 9 - writing help
The general structure for documenting help-info within the script is:

<#
.SYNOPSIS
{enter summary description here}
.DESCRIPTION
{enter more detailed description here}
.PARAMETER {parameter's name}
{enter description of the above parameter}
.PARAMETER {parameter's name}
{enter description of the above parameter}
.PARAMETER {parameter's name}
{enter description of the above parameter}
.EXAMPLE
{enter example here}
.EXAMPLE
{enter example here}
#>

For more info, check out:

help about-comment-based-help


Skipped section 8.2 which is about "xml-based help", which used to provide help info in multiple languages.