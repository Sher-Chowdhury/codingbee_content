---
ID: 291
post_title: 'PowerShell &#8211;  Using WinSCP to copy files to/from Linux machines'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-using-winscp-to-copy-files-tofrom-linux-machines
published: true
post_date: 2015-02-09 00:00:00
---
It is possible to copy files to/from a linux machine using Powershell. This can be done using a free tool called <a href="http://winscp.net/eng/index.php">winscp</a>. 

Winscp is actually a gui based tool, but you can use it from 




# I created this script using the following as an inspiration:
	# http://winscp.net/eng/docs/library_powershell#installing_the_assembly 
	
	# Load WinSCP .NET assembly
	[Reflection.Assembly]::LoadFrom("c:\path\to\WinSCPnet.dll") | Out-Null
	
	
	
	# Setup session options
	$sessionOptions = New-Object WinSCP.SessionOptions
	$sessionOptions.Protocol = [WinSCP.Protocol]::Sftp
	$sessionOptions.HostName = $ServerName
	$sessionOptions.UserName = $Username
	$sessionOptions.Password = $password
	#$sessionOptions.SshHostKeyFingerprint = "ssh-rsa 1024 xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx" # this feature is desabled in the next line. 
	# Note I disabled the above line, and enabled the line below using instructions from:
	# http://winscp.net/eng/docs/library_sessionoptions
	$sessionOptions.GiveUpSecurityAndAcceptAnySshHostKey = $true
	
	
	$session = New-Object WinSCP.Session
	# note for more info, see http://winscp.net/eng/docs/library_session
	
	# Connect
	$session.Open($sessionOptions)
	
	
	# Set transfer mode to binary
	# note, for more info, see:  http://winscp.net/eng/docs/library_transferoptions
	$transferOptions = New-Object WinSCP.TransferOptions
	$transferOptions.TransferMode = [WinSCP.TransferMode]::Binary



Now to send files to the linux machine, do:

<pre>
$session.PutFiles("$SourcePath", "$DestinationPath", $False, $transferOptions)
</pre>

And to retrieve files, do:

<pre>
$session.GetFiles("$SourcePath", "$DestinationPath", $False, $transferOptions)
</pre>


Putting it altogether, we have:


<pre>
[Reflection.Assembly]::LoadFrom(“C:\path\to\winscp-dll\WinSCPnet.dll”) | Out-Null

$sessionOptions = New-Object WinSCP.SessionOptions
$sessionOptions.Protocol = [WinSCP.Protocol]::Sftp
$sessionOptions.HostName = "linuxservername" 
$sessionOptions.UserName = "username"
$sessionOptions.Password = "password"

$sessionOptions.GiveUpSecurityAndAcceptAnySshHostKey = $true

$session = New-Object WinSCP.Session

$session.Open($sessionOptions)


$transferOptions = New-Object WinSCP.TransferOptions
$transferOptions.TransferMode = [WinSCP.TransferMode]::Binary

$SourcePath = "C:\Temp\testfile.txt"
$DestinationPath = "/tmp/testfile.txt"

$session.PutFiles("$SourcePath", "$DestinationPath", $False, $transferOptions)
</pre>

The folder that contains your WinSCPnet.dll file will also need to contain the WinSCP.exe file as well. You can download my copy of <a href="http://codingbee.net/wp-content/uploads/2015/02/winscp-dll.zip">both of these files</a>.  


<strong>Useful links:</strong>

http://winscp.net/eng/docs/library_sessionoptions#properties

http://winscp.net/eng/docs/library_powershell