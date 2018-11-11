---
ID: 61
post_title: 'PowerShell &#8211; Extend PowerShell&#8217;s capability by installing Modules and Snapins'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-extend-powershells-capability-by-installing-modules-and-snapins
published: true
post_date: 2014-05-11 00:00:00
---
When you install a new app on your smart phone, you actually ending enabling your phone to do more things that it originally couldn't do out of the box. In powershell we can add features (e.g. new cmdlets) to powershell in the same way, but by installing extensions. There are two types of extension that you install:

<ul>
	<li>PSSnapin</li>
	<li>modules </li>
</ul>

Modules are are better than PSSnapin, because they are more self-contained and easier to distribute, also they don't require advanced registration, instead you simply drop them in certain directories, which is covered later)

<h2>PSSnapins</h2>
first lets talk about PSSnapins

<pre>Get-PSSnapin -Registered</pre>

This lists all the available snapins that have been installed (regardless of whether they are active or not) but excludes snapins that came pre-installed with powershell.

Note, I think PSSnapin gets installed as part of a much larger software, e.g. things like microsoft sql developer, sharepoint,....etc. I don't think they come as standalones that you can download from the internet.

This lists all the snapins that are currently running (including snapins that came preinstalled with powershell):

<pre>Get-PSSnapin</pre>

If the above command shows something that you want to load, then you can load it using the following command:

<pre>
add-pssnapin -name {snapinname}
</pre>


Installing a snapin introduces one or both of the following:


<ul>
	<li>psdrive/psprovider</li>
	<li>cmdlets</li>
</ul>

To see what new commands a snapin has introduced, do:

<pre>Get-Command -name PSSnapin {snapin's name}</pre>

Note: the above doesn't work. 

Unfortunately there are no way to to see what psdrives have been added by a PSSnapin. The only way to check this by first running the following command before installing the PSSnapin:

<pre>
get-psprovider
</pre>


This should output something like:

<pre>
PS C:\> Get-PSProvider

Name                 Capabilities                                      Drives
----                 ------------                                      ------
WSMan                Credentials                                       {WSMan}
Alias                ShouldProcess                                     {Alias}
Environment          ShouldProcess                                     {Env}
FileSystem           Filter, ShouldProcess                             {C, E, A, D}
Function             ShouldProcess                                     {Function}
Registry             ShouldProcess, Transactions                       {HKLM, HKCU}
Variable             ShouldProcess                                     {Variable}
Certificate          ShouldProcess                                     {cert}
</pre>

And then run the above command again to see what new items are now listed.

Note: get-psprovider is what you use to start exploring the <a href="http://codingbee.net/tutorials/powershell/powershell-accessing-registry/" title="Powershell - Accessing the registry">registry</a>. This works in the same way as using regedit, but via the command line. 

############ detour - start
Note: A lot of softwares, e.g. scp comes with it's own command line shell. It could be that this command line shell is actually just a powershell
shell with the correct module/snapin preloaded. To check if that is really the case, right click the custom shell icon then do:

properties -=> find-target

Here you will find something like this:

%windir%\system32\WindowsPowershell\v1.0\powershell.exe -noexit -command import-module {module-name}
In these scenarios, instead of running this tool, you can simply open up powershell and run the "import-module {module-name}" command to achieve the same results.
############ detour - end

<h2>modules</h2>

Modules are designed to be a little more:

<ul>
	<li>self-contained</li>
	<li>easier to distribute</li>
	<li>dont require advanced registration</li>
</ul>

You can find and download modules from the internet, here are some popular module hosting websites:

<a href="http://powershell.codeplex.com/site/search?query=powershell&ac=3">http://powershell.codeplex.com/site/search?query=powershell&ac=3</a>

<a href="http://poshcode.org/">http://poshcode.org/</a>

Note, if you download them from the internet, then you first need to:

rightclick the zipped file -> properties -> unblock. 

Powershell automatically looks for available modules by looking at list of certain paths that are defined in the "psmodulepath" variable (which is stored in the "env" psdrive):

<pre>
Get-Content Env:\PSModulePath
C:\Documents and Settings\SChowdhury\My Documents\WindowsPowerShell\Modules;C:\WINDOWS\system32\WindowsPowerShell\v1.0\Modules\
</pre>

The cool thing about modules is that they are really easy to install (and activate). All you have to do is copy them into one of the folders defined in "Env:\PSModulePath".

Once you have placed the module into one of the "approved" folders, you can then check that the module has been installed and activated by running the following command:


<pre>
PS C:\> Get-Module -ListAvailable

Directory: C:\Users\SChowdhury\Documents\WindowsPowerShell\Modules

ModuleType Name ExportedCommands
---------- ---- ----------------
Script hmrctool {new-hmrcobject, get-maths}

Directory: C:\Program Files (x86)\PowerShell Community Extensions\Pscx3

ModuleType Name ExportedCommands
---------- ---- ----------------
Script Pscx {Add-PathVariable, Clear-MSMQueue, ConvertFrom-Base64, ConvertTo-Base64...}
</pre>


In the above example I also installed a really useful module called "Pscx" (short for "Powershell Community Extensions") which you can download from here:

http://pscx.codeplex.com/

Note: in this instance, the module is provided in the form of an msi file....which makes it even easier by walking you through an installation wizard. This msi file, behind the scene actually, use the unblock-file command (covered later), adds a new approved path to the $env:psmodulepath variable, and then place the module into that path.

Note: whenever you download a module from the internet, Windows tags these downaloads as "internet downloads" and fore safety+security reasons it will not automatically install+activate them. In this you can simply first use unblock-file command.

By the way, if you want to see what new commands are available, you can either do:


<pre>PS C:\> Get-Module -ListAvailable </pre>


This only gives a preview, so you can drill down like this:

<pre>
PS C:\> (Get-Module -ListAvailable | Where-Object -FilterScript {$_.Name -match "Pscx"}).ExportedCommands
</pre>



But a much better way is to simply do:


<pre>PS C:\> Get-Command -Module pscx

CommandType Name ModuleName
----------- ---- ----------
Alias Resize-Bitmap Pscx
Function Add-DirectoryLength Pscx
Function Add-ShortPath Pscx
Function Dismount-VHD Pscx
Function Edit-File Pscx
Function Edit-HostProfile Pscx
Function Edit-Profile Pscx
Function Enable-OpenPowerShellHere Pscx
Function Get-ExecutionTime Pscx
Function Get-Help Pscx
Function Get-Parameter Pscx
Function Get-ScreenCss Pscx
Function Get-ScreenHtml Pscx
Function Get-ViewDefinition Pscx
Function help Pscx
Function Import-VisualStudioVars Pscx
Function Invoke-BatchFile Pscx
Function Invoke-Elevated Pscx
Function Invoke-GC Pscx
Function Invoke-Method Pscx
Function Invoke-NullCoalescing Pscx
Function Invoke-Ternary Pscx
Function less Pscx
Function Mount-VHD Pscx
Function Out-Speech Pscx
Function QuoteList Pscx
Function QuoteString Pscx
Function Resolve-ErrorRecord Pscx
Function Resolve-HResult Pscx
Function Resolve-WindowsError Pscx
Function Set-LocationEx Pscx
Function Set-ReadOnly Pscx
Function Set-Writable Pscx
Function Show-Tree Pscx
Function Start-PowerShell Pscx
Function Stop-RemoteProcess Pscx
Filter New-HashObject Pscx
Cmdlet Add-PathVariable Pscx
Cmdlet Clear-MSMQueue Pscx
Cmdlet ConvertFrom-Base64 Pscx
Cmdlet ConvertTo-Base64 Pscx
Cmdlet ConvertTo-MacOs9LineEnding Pscx
Cmdlet ConvertTo-Metric Pscx
Cmdlet ConvertTo-UnixLineEnding Pscx
Cmdlet ConvertTo-WindowsLineEnding Pscx
Cmdlet Convert-Xml Pscx
Cmdlet Disconnect-TerminalSession Pscx
Cmdlet Expand-Archive Pscx
Cmdlet Export-Bitmap Pscx
Cmdlet Format-Byte Pscx
Cmdlet Format-Hex Pscx
Cmdlet Format-Xml Pscx
Cmdlet Get-ADObject Pscx
Cmdlet Get-AdoConnection Pscx
Cmdlet Get-AdoDataProvider Pscx
Cmdlet Get-Clipboard Pscx
Cmdlet Get-DhcpServer Pscx
Cmdlet Get-DomainController Pscx
Cmdlet Get-DriveInfo Pscx
Cmdlet Get-EnvironmentBlock Pscx
Cmdlet Get-FileTail Pscx
Cmdlet Get-FileVersionInfo Pscx
Cmdlet Get-ForegroundWindow Pscx
Cmdlet Get-Hash Pscx
Cmdlet Get-HttpResource Pscx
Cmdlet Get-LoremIpsum Pscx
Cmdlet Get-MountPoint Pscx
Cmdlet Get-MSMQueue Pscx
Cmdlet Get-OpticalDriveInfo Pscx
Cmdlet Get-PathVariable Pscx
Cmdlet Get-PEHeader Pscx
Cmdlet Get-Privilege Pscx
Cmdlet Get-PSSnapinHelp Pscx
Cmdlet Get-ReparsePoint Pscx
Cmdlet Get-RunningObject Pscx
Cmdlet Get-ShortPath Pscx
Cmdlet Get-TerminalSession Pscx
Cmdlet Get-TypeName Pscx
Cmdlet Get-Uptime Pscx
Cmdlet Import-Bitmap Pscx
Cmdlet Invoke-AdoCommand Pscx
Cmdlet Invoke-Apartment Pscx
Cmdlet Join-String Pscx
Cmdlet New-Hardlink Pscx
Cmdlet New-Junction Pscx
Cmdlet New-MSMQueue Pscx
Cmdlet New-Shortcut Pscx
Cmdlet New-Symlink Pscx
Cmdlet Out-Clipboard Pscx
Cmdlet Ping-Host Pscx
Cmdlet Pop-EnvironmentBlock Pscx
Cmdlet Push-EnvironmentBlock Pscx
Cmdlet Read-Archive Pscx
Cmdlet Receive-MSMQueue Pscx
Cmdlet Remove-MountPoint Pscx
Cmdlet Remove-ReparsePoint Pscx
Cmdlet Resolve-Host Pscx
Cmdlet Send-MSMQueue Pscx
Cmdlet Send-SmtpMail Pscx
Cmdlet Set-BitmapSize Pscx
Cmdlet Set-Clipboard Pscx
Cmdlet Set-FileTime Pscx
Cmdlet Set-ForegroundWindow Pscx
Cmdlet Set-PathVariable Pscx
Cmdlet Set-Privilege Pscx
Cmdlet Set-VolumeLabel Pscx
Cmdlet Skip-Object Pscx
Cmdlet Split-String Pscx
Cmdlet Stop-TerminalSession Pscx
Cmdlet Test-AlternateDataStream Pscx
Cmdlet Test-Assembly Pscx
Cmdlet Test-MSMQueue Pscx
Cmdlet Test-Script Pscx
Cmdlet Test-UserGroupMembership Pscx
Cmdlet Test-Xml Pscx
Cmdlet Write-BZip2 Pscx
Cmdlet Write-Clipboard Pscx
Cmdlet Write-GZip Pscx
Cmdlet Write-Tar Pscx
Cmdlet Write-Zip Pscx
</pre>



Note:  if you run just "get-module" it will only list modules that have been activated. This can be useful to identify any modules that have failed to load properly, as they won't appear in this list.