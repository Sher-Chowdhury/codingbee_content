---
ID: 102
post_title: 'PowerShell &#8211; Miscellaneous'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-miscellaneous
published: true
post_date: 2014-05-11 00:00:00
---




This is how you run bat scripts in ps:
& '.\mybatscript.cmd'
But this will also work:
.\mybatscript.cmd

Also you must create your cmd (bat) file using notepad, (and not notepad++), as advised here:
http://smallbusiness.chron.com/write-cmd-script-53226.html



http://searchwindowsserver.techtarget.com/tip/Top-25-Windows-PowerShell-commands-for-administrators
 
 
To open up the explorer window in the current directory, simply do:

explorer . 
 
 
 
 
The start-process command is used to start up gui based apps, e.g. :
 
Start-Process firefox
Start-Process notepad 
 
 
on windows xp, do start->run, then type "eventvwr"....good place for troubleshooting. 


Putty LINK - shortened to PLINK --- http://en.wikipedia.org/wiki/Plink




 
get-module -listavailable # this lists all the available modules
According to this link --- http://blog.richprescott.com/2012/02/powershell-training-back-to-basics.html --- it
says that if you want to access a given module, then you first have to install the actual applicaiton first, then 
refresh the module list by running get-module. Once they appear in the list, you then import them using the import-module command. 

Note: Some of the vendors are still providing their cmdlets in snap-ins, which is legacy for PowerShell v1.0. 


Automate web page interactions with powershell: 
https://www.google.co.uk/search?q=powershel+interact+with+web+pagesl&ie=utf-8&oe=utf-8&rls=org.mozilla:en-GB:official&client=firefox-a&gws_rd=cr
http://msdn.microsoft.com/en-us/magazine/cc337896.aspx
https://www.google.co.uk/search?q=powershel+interact+with+web+pagesl&ie=utf-8&oe=utf-8&rls=org.mozilla:en-GB:official&client=firefox-a&gws_rd=cr#client=firefox-a&rls=org.mozilla:en-GB%3Aofficial&sclient=psy-ab&q=powershell+read+a+web+page&oq=powershell+read+a+web+page&gs_l=serp.3..0i22i30l4.350311.354256.0.354465.15.13.0.2.2.0.165.1094.9j4.13.0....0...1c.1.21.psy-ab.L0xgP4I4eHI&pbx=1&bav=on.2,or.r_qf.&bvm=bv.49478099,d.d2k&fp=94de025e9fbfd016&biw=1280&bih=856 
http://www.google.co.uk/search?q=internet+explorer+powershell&sourceid=ie7&rls=com.microsoft:en-gb:IE-SearchBox&ie=&oe=&gws_rd=cr&ei=6nMLUqS-JY-V7AaTq4GICg
http://social.technet.microsoft.com/Forums/scriptcenter/en-US/9d003109-0903-414b-a635-ca2a9c485381/open-internet-explorer-8-and-its-tabs-using-powershell

For Bash, I think you could do similar things with curl and:
https://www.google.co.uk/search?q=bash+interact+with+web+pages&ie=utf-8&oe=utf-8&rls=org.mozilla:en-GB:official&client=firefox-a&gws_rd=cr#client=firefox-a&hs=U0X&rls=org.mozilla:en-GB%3Aofficial&sclient=psy-ab&q=use+bash+to+click+a+web+page+link+&oq=use+bash+to+click+a+web+page+link+&gs_l=serp.3...99346.111783.1.112059.41.32.6.3.5.2.108.2270.25j7.32.0....0...1c.1.21.psy-ab.Xe353crwGmI&pbx=1&bav=on.2,or.r_qf.&bvm=bv.49478099,d.d2k&fp=94de025e9fbfd016&biw=1280&bih=856
http://www.unix.com/shell-programming-scripting/125885-filling-out-web-form-script.html