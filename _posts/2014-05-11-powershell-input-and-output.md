---
ID: 72
post_title: 'PowerShell &#8211; Input and Output'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-input-and-output
published: true
post_date: 2014-05-11 00:00:00
---
Chapter 19 - Input and Output


In an earlier chapter we learned about write-host. This bypasses the invisible out-* sequence (as well as anything else in the pipeline)
and sends the output straight to the terminal screen:

get-service | out-default | out-host

write-host redirects the above to:

get-service | write-host

Write-host is mainly used for outputing warning messages, because write-host lets you change the color of your output. 

Get-Service | Write-Host -BackgroundColor black -ForegroundColor red

Hence this is useful for displaying warning messages to the user. Unfortunetly you can use this technique to add color to log files, 
because the message gets outputed to the screen rather than a terminal. 

In reality you will very rarely use the write-host command.

If you want to write confirmation messages, then a better command to use is "write-verbose", e.g.:

Write-Verbose -message "Server connection successful" -Verbose  	

# Note, you have to use the verbose switch, becuase the default is to do it silently. You can change this by changing the following 
variable:

PS C:\> $VerbosePreference
SilentlyContinue
PS C:\> $VerbosePreference = "continue"
PS C:\> $VerbosePreference
Continue
PS C:\> Write-Verbose -message "Server connection successful"  		# notice we dont need to use the verbose switch anymore.   
VERBOSE: Server connection successful

However "write-verbose" is most often written in scripts, and activated during the script's execution, like this:

PS C:\> {\path\to\script} -verbose

This is possible because "-verbose" is actually one of the common parameters. This is a better option then constantly changing the value of $VerbosePreference. 


There is also "write-output". This actually feeds data into the pipeline. In other words, the following 2 commands do the same thing:

PS C:\> write-output "hello world"
hello world
PS C:\> "hello world"			# Here the write-output declaration is invisible (along with out-default and out-host). 
hello world

Note: "write-output" essentially the Linux equivaelent of the "echo" command.

write-output is actually the shells default command. when you tell the shell to do something that isn't a command, then the shell automatically passes 
whatever you typed to write-output (behind the scenes).

There are a few other write-* commands that also behaves like write-host (i.e. also bypasses out-default and out-host, as well as anything
else in the pipeline):



write-warning	# Displays warning message in yellow font with "WARNING:" label preceding it. 
				# It has an associated "$warningpreference" variable setting, that can be set to "continue" or "silentlycontinue"
				# "continue" is the default setting.
				
write-verbose	# Displays "additional info" message in yellow font with "VERBOSE:" label preceding it. 
				# It has an associated "$verbosepreference" variable setting, that can be set to "continue" or "silentlycontinue"
				# "silentlycontinue" is the default setting.
				# You can also enable when running a single script by using the "-verbose" common parameter.
				# This command is useful for out debugging message such as the linux equivalent of "echo line256".

write-debug		# Displays debug message in yellow font with "DEBUG:" label preceding it. 
				# It has an associated "$debugpreference" variable setting, that can be set to "continue" or "silentlycontinue"
				# "silentlycontinue" is the default setting.
				# You can also enable when running a single script by using the "-debug" common parameter.
				# You will discover later that this command is the linux equivalent of the "read" command, 
				# in terms of pausing a script for debugging. 

write-error		# Displays warning message in yellow font with "ERROR:" label preceding it. 
				# It has an associated "$ErrorActionPreference" variable setting, that can be set to "continue" or "silentlycontinue"
				# "continue" is the default setting
				# This also writes an error to the Powershell's error stream. 

There is also another write-* command called "write-progress". This gives a progress bar when running really long scripts, 
you can learn more about it (when the time comes) here:

Here is a sample write-progress script that you can try:


	for ($a=0; $a -lt 10; $a++) {
		Write-Progress -id 1 -Activity "Installing components into Pool D" -PercentComplete $($a*10) -CurrentOperation "copying file xyz" -Status "completed mini task $a"
		Start-Sleep 1
	}
	Write-Progress -id 1 -Activity "Pool D install has completed" -PercentComplete 100 -Status "Everything installed successfully."
	start-sleep 10
	## The following line forces the progress bar to disappear after the 10 second sleep is over.  
	Write-Progress -id 1 -Activity "Installing components into Pool D" -Status "Everything installed successfully." -Completed

For more info, check out:

http://technet.microsoft.com/en-gb/magazine/2008.03.powershell.aspx
http://www.hanselman.com/blog/ProgressBarsInPowerShell.aspx				# This shows how to create a 2 level progress bar.