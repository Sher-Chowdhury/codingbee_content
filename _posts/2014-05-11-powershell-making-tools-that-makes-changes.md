---
ID: 88
post_title: 'PowerShell &#8211; Making tools that makes changes'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-making-tools-that-makes-changes
published: true
post_date: 2014-05-11 00:00:00
---
Note: skipped ch14 this because it was about db access, using pre-written scripts in .net. Also skipped ch 15 because it is an interlude that didn't cover anythign new

Chapter 16 - Making tools that makes changes

Awhile back you came across "-whatif". You can enable these features in your scripts/functions too by simply updating your "[cmdletbinding()]" with the following:

[cmdletbinding(SupportsShouldProcess=$True)]

By enabling this, you script/function will now have the "-whatif" and "-confirm" options available on the commnand line. 

In the background, what actually happens is that the -whatif requests gets passed down the to the lower level powershell commands who take on the responsibilities of reporting back. 


Now you will find a powershell preference setting called:

PS C:\> $WhatIfPreference
False

If you change this to true:

PS C:\> $WhatIfPreference=$True
PS C:\> $WhatIfPreference
True

Then it means that the "-whatif" option is implicitly always switched-on for every command, in other words it puts the whole powershell into "whatif" mode:

PS C:\> get-Service | Where-Object -FilterScript {$_.name -match "audio"}
Status   Name               DisplayName
------   ----               -----------
Running  AudioSrv           Windows Audio
PS C:\> Stop-Service AudioSrv
What if: Performing operation "Stop-Service" on Target "Windows Audio (AudioSrv)".


Note: Not all commands are whatif compatible, and those that aren't will run as normal. 

If you are in whatif mode and you want to suppress "whatif" when running a command, then you either have to change the preference, or you can do:

PS C:\> Stop-Service AudioSrv -WhatIf:$false		# This will temporarily switch of whatif just for this command only. 

PS C:\> get-Service | Where-Object -FilterScript {$_.name -match "audio"}
Status   Name               DisplayName
------   ----               -----------
Stopped  AudioSrv           Windows Audio 

 
Next we have the following preference setting:

PS C:\> $ConfirmPreference
High

You can think of this as a tolerance level, and this preference has 3 settings:

	- High
	- Medium
	- Low
	
Each ps command has a builtin $ConfirmPreference value, if the command's value is higher then the PS's global value, then the "-confirm" option for that command will automatically be activated (regardless of whether or not you typed in "-confirm" on the command line).

By default, the global setting is set to "High", which means you shouldn't ever get any confirm popup windows. However if you change this to low, and then try to run a command, then the "-confirm" comes into action:

PS C:\> $ConfirmPreference='low'
PS C:\> $ConfirmPreference
Low
PS C:\> Stop-Service AudioSrv

Confirm
Are you sure you want to perform this action?
Performing operation "Stop-Service" on Target "Windows Audio (AudioSrv)".
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):


You can also suppress this auto-confirm behaviour, like this:

Start-Service AudioSrv -Confirm:$false


You can also add a built-in confirmpreference value into your script, which again is done using cmdletbinding:

[cmdletbinding(SupportsShouldProcess=$True,		# This enables the -whatif and -confirm switches
				ConfirmImpact='High')]			# This attaches the builtin "ConfirmImpact" value which can automatically trigger "-confirm"
												# if this value is higher than the global $ConfirmPreference value. 

Note, it is up to you, on  what ConfirmImpact rating you want to give to your scripts/functions, however if $ConfirmPreference is already set to "high", then your scripts/functions ConfirmImpact ratings will never come into play. 

For more info, checkout:

about_Functions_CmdletBindingAttribute


Sometimes you will want to tell your command to only do "-confirm" at a certain part within a function....and then you want the script to respond differently depending on option you pick. In otherwords create something that is the linux equivalent of the "read" command combined with an if-else statement. Here is oneway that this can be done:

$hmrclogfile='c:/temp/hmrclogfile.txt'

function new-hmrcobject {

	[cmdletbinding(ConfirmImpact='High')]		# You need to have this option enabled in order for this work. 

   	param(
	[string[]]$computername 
   	)

	"You are are about to over-write the log file!"
    if ($pscmdlet.ShouldProcess('Are you sure that you want to overwrite the log file?'))		# See note1 below.
		{
        "This is the first line in the logfile" | out-file -filepath $hmrclogfile
		}
    else 
		{
        "exiting because failed to write to the log file."
        return
		}
    "Log file created successfully."

    "Here I am appending another line to the log file." | out-file -filepath $hmrclogfile -append
}

new-hmrcobject -computername test 
 
 
Note1: The cmdletbinding feature comes with a special variable-object called $pscmdlet. This $pscmdlet represent is a variable-object representation of your function (which in this case is "new-hmrcobject"), and you can access the methods of $pscmdlet to control how your functions behaves. In this instance we have accessed the "ShouldProcess" method which results in the "-confirm" window popping. Now depending on which button 
you click, this will either evaluate to True or False....which in turn will trigger either the "if" or "else" part of the conditional block.