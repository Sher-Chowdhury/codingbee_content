---
ID: 95
post_title: 'PowerShell &#8211;  Web automation'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-web-automation
published: true
post_date: 2014-05-11 00:00:00
---
Note: You should use selenium rather than powershell for web autoamation, that's because it is easier. 

This script automatically logs into hotmail:

############################################## Start script



$ie = new-object -com "InternetExplorer.Application"

$ie.Visible = $true

$ie.Navigate("https://login.live.com/login.srf?wa=wsignin1.0&rpsnv=11&ct=1348676581&rver=6.1.6206.0&wp=MBI&wreply=http:%2F%2Fmail.live.com%2Fdefault.aspx&lc=2057&id=64855&mkt=en-gb&cbcxt=mai&snsc=1")

while ($ie.Busy -eq $true)

{

      Start-Sleep -Milliseconds 1000;

}

$ie.Document.getElementById("login").value ="ihkhan@hotmail.co.uk"

$ie.Document.getElementById("passwd").value ="2222"

sleep -Seconds 5

$ie.Document.getElementById("SI").click()

sleep -Seconds 2

$ie.Document.getElementById("msgChkAll").click() 
 

while ($ie.Busy -eq $true)

{

      Start-Sleep -Milliseconds 1000;
}


############################################## end script

-------------------------------------- End Detour

I think the following link helps to explain how the above script works:
http://msdn.microsoft.com/en-us/magazine/cc337896.aspx

Here is an alternative approach using a new psv3 command called invoke-webrequest
http://www.powershellcookbook.com/recipe/vODQ/script-a-web-application-session


At work, we interact with web pages using a lot of .net components. However psv3 introduced the "invoke-webrequest" which I think gives an easier approach
to automate web tasks.