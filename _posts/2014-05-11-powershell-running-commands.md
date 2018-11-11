---
ID: 58
post_title: 'PowerShell &#8211; Running commands'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-running-commands
published: true
post_date: 2014-05-11 00:00:00
---
There are different types of commands that you can run on the PowerShell Command line:
<ul>
	<li>cmdlet - Name for any powershell builtin command, e.g. get-service, set-location, get-childitem....etc</li>
	<li>aliases - these are nicknames for other commands. You can view a list of aliases using get-alias. It's best to avoid aliases where possible.</li>
	<li>function - Small blocks of powershell codes that are found in powershell scripts. You can also load a function into the current powershell session so that you can call the function in the same was as an ordinary cmdlet. A PowerShell   session comes preloaded with a default set of functions, for example the "help" function that we saw earlier.</li>
	<li>workflow - A special kind of function, which we'll cover later on.</li>
	<li>application - an external application. E.g. try running "notepad" or "mspaint" straight from the command line.</li>
</ul>
The word "command" is a generic term that and this course is used to refer to any of the above.

There are a large number of aliases you can use instead of the typing out the whole cmdlet, here are some commonly used aliases:


<pre>CommandType     Name                                               ModuleName
-----------     ----                                               ----------
Alias           % -=> ForEach-Object
Alias           ? -=> Where-Object
Alias           ac -=> Add-Content
Alias           asnp -=> Add-PSSnapin
Alias           cat -=> Get-Content
Alias           cd -=> Set-Location
Alias           chdir -=> Set-Location
Alias           clc -=> Clear-Content
Alias           clear -=> Clear-Host
Alias           clhy -=> Clear-History
Alias           cli -=> Clear-Item
Alias           clp -=> Clear-ItemProperty
Alias           cls -=> Clear-Host
Alias           clv -=> Clear-Variable
Alias           cnsn -=> Connect-PSSession
Alias           compare -=> Compare-Object
Alias           copy -=> Copy-Item
Alias           cp -=> Copy-Item
Alias           cpi -=> Copy-Item
Alias           cpp -=> Copy-ItemProperty
Alias           curl -=> Invoke-WebRequest
Alias           cvpa -=> Convert-Path
Alias           dbp -=> Disable-PSBreakpoint
Alias           del -=> Remove-Item
Alias           diff -=> Compare-Object
Alias           dir -=> Get-ChildItem
Alias           dnsn -=> Disconnect-PSSession
Alias           ebp -=> Enable-PSBreakpoint
Alias           echo -=> Write-Output
Alias           epal -=> Export-Alias
Alias           epcsv -=> Export-Csv
Alias           epsn -=> Export-PSSession
Alias           erase -=> Remove-Item
Alias           etsn -=> Enter-PSSession
Alias           exsn -=> Exit-PSSession
Alias           fc -=> Format-Custom
Alias           fl -=> Format-List
Alias           foreach -=> ForEach-Object
Alias           ft -=> Format-Table
Alias           fw -=> Format-Wide
Alias           gal -=> Get-Alias
Alias           gbp -=> Get-PSBreakpoint
Alias           gc -=> Get-Content
Alias           gci -=> Get-ChildItem
Alias           gcm -=> Get-Command
Alias           gcs -=> Get-PSCallStack
Alias           gdr -=> Get-PSDrive
Alias           ghy -=> Get-History
Alias           gi -=> Get-Item
Alias           gjb -=> Get-Job
Alias           gl -=> Get-Location
Alias           gm -=> Get-Member
Alias           gmo -=> Get-Module
Alias           gp -=> Get-ItemProperty
Alias           gps -=> Get-Process
Alias           group -=> Group-Object
Alias           gsn -=> Get-PSSession
Alias           gsnp -=> Get-PSSnapin
Alias           gsv -=> Get-Service
Alias           gu -=> Get-Unique
Alias           gv -=> Get-Variable
Alias           gwmi -=> Get-WmiObject
Alias           h -=> Get-History
Alias           history -=> Get-History
Alias           icm -=> Invoke-Command
Alias           iex -=> Invoke-Expression
Alias           ihy -=> Invoke-History
Alias           ii -=> Invoke-Item
Alias           ipal -=> Import-Alias
Alias           ipcsv -=> Import-Csv
Alias           ipmo -=> Import-Module
Alias           ipsn -=> Import-PSSession
Alias           irm -=> Invoke-RestMethod
Alias           ise -=> powershell_ise.exe
Alias           iwmi -=> Invoke-WmiMethod
Alias           iwr -=> Invoke-WebRequest
Alias           kill -=> Stop-Process
Alias           lp -=> Out-Printer
Alias           ls -=> Get-ChildItem
Alias           man -=> help
Alias           md -=> mkdir
Alias           measure -=> Measure-Object
Alias           mi -=> Move-Item
Alias           mount -=> New-PSDrive
Alias           move -=> Move-Item
Alias           mp -=> Move-ItemProperty
Alias           mv -=> Move-Item
Alias           nal -=> New-Alias
Alias           ndr -=> New-PSDrive
Alias           ni -=> New-Item
Alias           nmo -=> New-Module
Alias           npssc -=> New-PSSessionConfigurationFile
Alias           nsn -=> New-PSSession
Alias           nv -=> New-Variable
Alias           ogv -=> Out-GridView
Alias           oh -=> Out-Host
Alias           popd -=> Pop-Location
Alias           ps -=> Get-Process
Alias           pushd -=> Push-Location
Alias           pwd -=> Get-Location
Alias           r -=> Invoke-History
Alias           rbp -=> Remove-PSBreakpoint
Alias           rcjb -=> Receive-Job
Alias           rcsn -=> Receive-PSSession
Alias           rd -=> Remove-Item
Alias           rdr -=> Remove-PSDrive
Alias           ren -=> Rename-Item
Alias           ri -=> Remove-Item
Alias           rjb -=> Remove-Job
Alias           rm -=> Remove-Item
Alias           rmdir -=> Remove-Item
Alias           rmo -=> Remove-Module
Alias           rni -=> Rename-Item
Alias           rnp -=> Rename-ItemProperty
Alias           rp -=> Remove-ItemProperty
Alias           rsn -=> Remove-PSSession
Alias           rsnp -=> Remove-PSSnapin
Alias           rujb -=> Resume-Job
Alias           rv -=> Remove-Variable
Alias           rvpa -=> Resolve-Path
Alias           rwmi -=> Remove-WmiObject
Alias           sajb -=> Start-Job
Alias           sal -=> Set-Alias
Alias           saps -=> Start-Process
Alias           sasv -=> Start-Service
Alias           sbp -=> Set-PSBreakpoint
Alias           sc -=> Set-Content
Alias           select -=> Select-Object
Alias           set -=> Set-Variable
Alias           shcm -=> Show-Command
Alias           si -=> Set-Item
Alias           sl -=> Set-Location
Alias           sleep -=> Start-Sleep
Alias           sls -=> Select-String
Alias           sort -=> Sort-Object
Alias           sp -=> Set-ItemProperty
Alias           spjb -=> Stop-Job
Alias           spps -=> Stop-Process
Alias           spsv -=> Stop-Service
Alias           start -=> Start-Process
Alias           sujb -=> Suspend-Job
Alias           sv -=> Set-Variable
Alias           swmi -=> Set-WmiInstance
Alias           tee -=> Tee-Object
Alias           trcm -=> Trace-Command
Alias           type -=> Get-Content
Alias           wget -=> Invoke-WebRequest
Alias           where -=> Where-Object
Alias           wjb -=> Wait-Job
Alias           write -=> Write-Output
</pre>

If you have experience in using the linux command line, then a lot of these aliases will be familiar to you.

Sometime it can be difficult to write a command even after reading the help pages, that's why powershell comes with a gui wizard in the form of show-command that helps you to write your commands. E.g. if you need help writing a move-item command, then just do:


<pre>show-command -Name Move-Item</pre>