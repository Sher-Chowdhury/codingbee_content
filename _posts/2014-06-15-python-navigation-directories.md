---
ID: 119
post_title: 'Python &#8211; Navigation directories'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/python/python-navigation-directories
published: true
post_date: 2014-06-15 00:00:00
---
If you want to do pwd, cd, and ls, then you need to import the os module first:


[python]
import os
[/python]


Now we have the following functions:

[python]
=>=>=> os.getcwd()         # equivalent to pwd
'C:\\users\\mir'
=>=>=> os.chdir(&quot;C:/&quot;)     # equivalent to cd
=>=>=> os.listdir()        # equivalent to ls
['$AVG', '$Recycle.Bin', 'Documents and Settings', 'hiberfil.sys', 'inetpub', 'Intel', 'NV
IDIA', 'pagefile.sys', 'PerfLogs', 'Prey', 'Program Files', 'Program Files (x86)', 'Progra
mData', 'Python34', 'Recovery', 'System Volume Information', 'Temp', 'Users', 'Windows']
=>=>=>
[/python]