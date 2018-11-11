---
ID: 108
post_title: 'PowerShell &#8211; Use CredSSP to run commands remotely with fewer issues'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-use-credssp-to-run-commands-remotely-with-fewer-issues
published: true
post_date: 2014-06-04 00:00:00
---
The "invoke-command" cmdlet is specifically used to send commands to remote windows machines (aka servers), using your local workstation (aka client).

In order to do this, run the following command on both client and servers machines, if you haven't already done so:


<pre>enable-psremoting -force
</pre>
This commands tells the machine that it can now run an commands it receives from somewhere.

Once you have done this, you can then use the client machine to send commands to be run on other machines, using invoke-command:

<pre>
Invoke-Command -ComputerName RemoteServerName -ScriptBlock {
   get-childitem
   get-service
}
</pre>

When you run this, you should get a prompt to enter valid username and password, in order for the invoke-command to first authenticate against the server, before the server can accept the commands and run them. However this can be a problem if you want to run this script from inside an powershell-script, which has to be run non-interactively. The answer to this problem is to use the invoke-command cmdlet's "-credential" parameter. You need to feed in a "pscredential" object into the credential parameter in order for this to work. pscredentials are basically an object that stores your username, and an encrypted version of your password. You can create this object like this:

<pre>
# Set up credentials.
$password =  ConvertTo-SecureString -String $env:Password -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential $env:Username,$password
</pre>

After that, you can then use your credential like a door-key like this:

<pre>
Invoke-Command -ComputerName RemoteServerName -credential $cred -ScriptBlock {
   get-service
}
</pre>

This time, invoke-command will run non-iteractively, and won't prompt you for username/password. That means that you can now use invoke-command inside your powershell scripts, as long as you use the -credential option.

One thing you might want to do is have your pscredential always available to you in the form of a <a title="PowerShell - Environment Variables" href="http://codingbee.net/tutorials/powershell/powershell-environment-variables/">PowerShell Environment Variable</a> (look up the section about export-clixml).

However invoke-command by default runs commands with a low privilege level, so some commands may fail to run, unless you run the commands by logging into the server directly. Here is an example of a command that will fail:

<pre>
# Set up credentials.
$password =  ConvertTo-SecureString -String $env:Password -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential $env:Username,$password 

Invoke-Command -ComputerName RemoteServerName -Credential $cred -ScriptBlock {
   get-childitem \\path\to\another\remove\server\within\the\same\domain
}
</pre>

This will fail because you are hopping from machine1 to machine2, and then hopping from machine2 to machine3. This is something that isn't allowed with lowered privileges. The answer to this problem is to use "credssp". Using invoke-command along with "CredSSP" will really help avoid various privilege related issues:

<pre>
PS C:\WINDOWS\system32=> Get-WSManCredSSP

The machine is not configured to allow delegating fresh credentials.
This computer is configured to receive credentials from a remote client computer.
</pre>


<pre>
Enable-WSManCredSSP -Role Client -DelegateComputer {domain-name}
</pre>

However I noticed that didn't work, instead I had to use the fully qualified machine name, which is:

<pre>
{$env:COMPUTERNAME}.{$env:USERDNSDOMAIN}
</pre>

The Enable-WSManCredSSP cmdlet needs to be run on the central machine that will be controlling all the other machines. Hence you only need to run this command on the one machine. After running this command you might need to wait a few minutes before you do anything else. This command could also stop the winRM service running, so might need to enable it again by running the enable-psremoting command again. The output should be:

<pre>
Enable-WSManCredSSP -Role Client -DelegateComputer {domain-name}

cfg         : http://schemas.microsoft.com/wbem/wsman/1/config/client/auth
lang        : en-US
Basic       : Basic
Digest      : Digest
Kerberos    : true
Negotiate   : true
Certificate : true
CredSSP     : CredSSP
</pre>

Also you can now confirm this as follows:

<pre>
PS Env:\=> get-WSManCredSSP
The machine is configured to allow delegating fresh credentials to the following target(s): wsman/{domain-name}
This computer is configured to receive credentials from a remote client computer.
</pre>

&nbsp;

On all the other machines that you want to control, you need to access the windows desktops in turn, open up powershell from the start menue, and then run the following:

<pre>
PS C:\Windows\system32=> Enable-WSManCredSSP -Role Server

cfg               : http://schemas.microsoft.com/wbem/wsman/1/config/service/auth
lang              : en-US
Basic             : false
Kerberos          : true
Negotiate         : true
Certificate       : false
CredSSP           : true
CbtHardeningLevel : Relaxed

</pre>

After that, you should see:

<pre>
PS C:\Windows\system32=> Get-WSManCredSSP
The machine is not configured to allow delegating fresh credentials.
This computer is configured to receive credentials from a remote client computer.
</pre>

Now you can run the invoke-command inside a ps1 script with the credssp mode switched on (using the authentication parameter):

<pre>
# Set up credentials.
$password =  ConvertTo-SecureString -String $env:Password -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential $env:Username,$password 

Invoke-Command -ComputerName MachineName.domain.com -Authentication CredSSP  -Credential $cred -ScriptBlock {
   get-childitem \\path\to\remove\server\within\the\same\domain
}
</pre>

Note: When connecting to a remote machine using invoke-command and credssp enabled, you also need to specify the full machine name.

Another way to test, is by creating an interactive session like this:

<pre>
Enter-PSSession -ComputerName machine.domain.com -Credential $cred -Authentication Credssp
</pre>

Note, in the above examples we configured remote machines manually by via remote desktoping. However it is possible to do everything from the remote command line using the <a title="PowerShell - Using psexec to automate UI tasks on remote machines" href="http://codingbee.net/tutorials/powershell/powershell-using-psexec-automate-ui-tasks-remote-machines/">psexec command-line utility</a>.

After you have done everything above, you migth still have issues connecting to the target machine, and you might get a error message like this:

&nbsp;

<a href="http://codingbee.net/wp-content/uploads/2014/06/credsspError.png"><img class="alignnone size-full wp-image-1103" src="http://codingbee.net/wp-content/uploads/2014/06/credsspError.png" alt="credsspError" width="844" height="232" /></a>

This error message is quite informative, and can help to fix the issue, so if you get this message, you need to do the following on the client machine:

&nbsp;
<ol>
	<li>start | run "gpedit.msc"</li>
	<li>drill down to Computer-configuration | administrative templates | system | credential delegation</li>
	<li>double click on "Allow Delgating Fresh Credentials with NTLM-only server authentication"</li>
	<li>Enable this option</li>
	<li>click on the show button</li>
	<li>add in "WSMAN/*.domain.co.uk"</li>
</ol>
&nbsp;

&nbsp;

Useful links:
http://social.technet.microsoft.com/Forums/windowsserver/en-US/e2b59f03-b49f-4432-ba7b-86cd74e1d392/remote-execution-of-a-script-with-getchild-item-cannot-find-path?forum=ITCG

http://dustinhatch.tumblr.com/post/24589312635/enable-powershell-remoting-with-credssp-using-group

http://technet.microsoft.com/en-us/library/hh849871.aspx

http://technet.microsoft.com/en-us/library/hh849876.aspx

http://stackoverflow.com/questions/2248093/run-command-as-administrator-in-powershell-script-uac

http://stackoverflow.com/questions/2248093/run-command-as-administrator-in-powershell-script-uac

http://blogs.technet.com/b/heyscriptingguy/archive/2011/05/11/check-for-admin-credentials-in-a-powershell-script.aspx (this is useful)

http://blogs.msdn.com/b/virtual_pc_guy/archive/2010/09/23/a-self-elevating-powershell-script.aspx

http://www.ravichaganti.com/blog/?p=1230

http://stackoverflow.com/questions/2248093/run-command-as-administrator-in-powershell-script-uac

&nbsp;

http://www.thomas-franke.net/powershell-manage-passwords-credentials/       (this tells you how to store the pscredential object as an xml file)

http://searchwindowsserver.techtarget.com/answer/Why-does-a-PowerShell-command-run-fine-locally-but-not-remotely

&nbsp;

http://powershell.org/wp/ebooks/

&nbsp;

Also do some research on:

http://technet.microsoft.com/en-us/library/cc754243.aspx