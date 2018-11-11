---
ID: 128
post_title: 'PowerShell &#8211; IIS automation'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-iis-automation
published: true
post_date: 2014-07-16 00:00:00
---
You can view the IIS gui conole by running "Inetmgr" on the commandline.

&nbsp;

&nbsp;

To automate IIS you first need to ensure you have the "webadministration":

&nbsp;

[powershell]
PS C:\Windows\system32&gt; Get-Module -ListAvailable

    Directory: C:\Windows\system32\WindowsPowerShell\v1.0\Modules

ModuleType Version    Name                                ExportedCommands
---------- -------    ----                                ----------------
Manifest   1.0.0.0    ADRMS                               {Update-ADRMS, Uninstall-ADRMS, Install-ADRMS}
Manifest   1.0.0.0    AppLocker                           {Set-AppLockerPolicy, Get-AppLockerPolicy, Test-AppLockerP...
Manifest   1.0        BestPractices                       {Get-BpaModel, Invoke-BpaModel, Get-BpaResult, Set-BpaResult}
Manifest   1.0.0.0    BitsTransfer                        {Add-BitsFile, Remove-BitsTransfer, Complete-BitsTransfer,...
Manifest   1.0.0.0    CimCmdlets                          {Get-CimAssociatedInstance, Get-CimClass, Get-CimInstance,...
Script     1.0.0.0    ISE                                 {New-IseSnippet, Import-IseSnippet, Get-IseSnippet}
Manifest   3.0.0.0    Microsoft.PowerShell.Diagnostics    {Get-WinEvent, Get-Counter, Import-Counter, Export-Counter...
Manifest   3.0.0.0    Microsoft.PowerShell.Host           {Start-Transcript, Stop-Transcript}
Manifest   3.1.0.0    Microsoft.PowerShell.Management     {Add-Content, Clear-Content, Clear-ItemProperty, Join-Path...
Manifest   3.0.0.0    Microsoft.PowerShell.Security       {Get-Acl, Set-Acl, Get-PfxCertificate, Get-Credential...}
Manifest   3.1.0.0    Microsoft.PowerShell.Utility        {Format-List, Format-Custom, Format-Table, Format-Wide...}
Manifest   3.0.0.0    Microsoft.WSMan.Management          {Disable-WSManCredSSP, Enable-WSManCredSSP, Get-WSManCredS...
Binary     1.0        PSDesiredStateConfiguration         {Set-DscLocalConfigurationManager, Start-DscConfiguration,...
Script     1.0.0.0    PSDiagnostics                       {Disable-PSTrace, Disable-PSWSManCombinedTrace, Disable-WS...
Binary     1.1.0.0    PSScheduledJob                      {New-JobTrigger, Add-JobTrigger, Remove-JobTrigger, Get-Jo...
Manifest   2.0.0.0    PSWorkflow                          {New-PSWorkflowExecutionOption, New-PSWorkflowSession, nwsn}
Manifest   1.0.0.0    PSWorkflowUtility                   Invoke-AsWorkflow
Manifest   1.0.0.0    ServerManager                       {Get-WindowsFeature, Add-WindowsFeature, Remove-WindowsFea...
Manifest   1.0.0.0    TroubleshootingPack                 {Get-TroubleshootingPack, Invoke-TroubleshootingPack}
Manifest   1.0.0.0    WebAdministration                   {Start-WebCommitDelay, Stop-WebCommitDelay, Get-WebConfigu...

    Directory: C:\Program Files\System Center Operations Manager\Agent\PowerShell

ModuleType Version    Name                                ExportedCommands
---------- -------    ----                                ----------------
Binary     0.0.0.0    Microsoft.MonitoringAgent.PowerS... {Checkpoint-WebApplicationMonitoring, Get-WebApplicationMo...

PS C:\Windows\system32&gt;
[/powershell]

&nbsp;

If you have windows 2008 with IIS 7.5 installed, then you should see a module called "WebAdministration". Next do:

&nbsp;

[powershell]
Import-Module -Name WebAdministration
[/powershell]

After that you should have a bunch of IIS related commands ready for use:

[powershell]
PS C:\Windows\system32&gt; Get-Command -Module webadministration

CommandType     Name                                               ModuleName
-----------     ----                                               ----------
Alias           Begin-WebCommitDelay                               webadministration
Alias           End-WebCommitDelay                                 webadministration
Function        IIS:                                               webadministration
Cmdlet          Add-WebConfiguration                               webadministration
Cmdlet          Add-WebConfigurationLock                           webadministration
Cmdlet          Add-WebConfigurationProperty                       webadministration
Cmdlet          Backup-WebConfiguration                            webadministration
Cmdlet          Clear-WebConfiguration                             webadministration
Cmdlet          Clear-WebRequestTracingSettings                    webadministration
Cmdlet          ConvertTo-WebApplication                           webadministration
Cmdlet          Disable-WebGlobalModule                            webadministration
.
.
.
.
[/powershell]

Along with this you should have the IIS drive available too:

[powershell] PS C:\Windows\system32&gt; psdrive Name Used (GB) Free (GB) Provider Root CurrentLocation ---- --------- --------- -------- ---- --------------- A FileSystem A:\ Alias Alias C 30.80 19.10 FileSystem C:\ Windows\system32 Cert Certificate \ D FileSystem D:\ Env Environment Function Function HKCU Registry HKEY_CURRENT_USER HKLM Registry HKEY_LOCAL_MACHINE IIS WebAdminis... \\OSVM1043 Variable Variable WSMan WSMan [/powershell]

<h2>  Create a Virtual Directory</h2>
You need to use the New-WebVirtualDirectory command:

&nbsp;

http://www.jasonholden.com/blog/index.cfm/2014/1/7/Use-PowerShell-to-add-IIS-virtual-directory-with-Login-Connect-As

&nbsp;

Here is an example command:

New-WebVirtualDirectory -Name "xxx"   -site "Default Web Site" -PhysicalPath 'C:\Program Files\path\to\folder'

&nbsp;

Name - This is the name as you want it to when viewing it in the Inetmgr's tree structure.

Sites - on the inetmgr gui, it is the parent too the name, within the tree.

physicalpath - it is the full path to the folder where the folder should reside. For consistency it might be a good idea to mirror the folder's name with the "name"

&nbsp;

&nbsp;

&nbsp;

<strong>Resources</strong>

http://stackoverflow.com/questions/5963647/webadministration-powershell-module-not-found-on-windows-server-data-center

&nbsp;

http://www.iis.net/learn/manage/powershell/powershell-snap-in-creating-web-sites-web-applications-virtual-directories-and-application-pools

&nbsp;

http://stackoverflow.com/questions/14236406/how-to-enable-a-windows-feature-via-powershell

&nbsp;

&nbsp;

After this, you should see IIS when you do:

get-psprovider

Also you should see the following new commands:

Get-Command -Module webadministration

&nbsp;

&nbsp;

&nbsp;