---
ID: 82
post_title: 'PowerShell &#8211; Advanced Functions (part 2)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-advanced-functions-part-2
published: true
post_date: 2014-05-11 00:00:00
---
Chapter 8 - Advanced functions, part 2


you will recall that in ps, the output of a whole script is fed through a single pipeline, which in turn is passed through out-host which formats the output before being displayed on screen. Since out-host can only retrieve formatting information of one kind of object per pipeline, it means that it is best practice to follow the output-only-one-kind-of-object per script rule, to avoid incorrectly formatted outputs. However there is an 
exception to this rule, which is "write-verbose". anything outputted by write-verbose has it's own special separate pipeline. This makes write-verbose perfect for outputting debugging messages, e.g.:

write-verbose "line2808" 
   
The above output will not conflict with the the scripts, main output. 

By default write-verbose will not output anything, unless both of the following is true:
	- you type (the common parameter,) "-verbose" as one of the parameters in the script. This will switch-on the write-verbose mode. 
	- You have inserted "[CmdletBinding()]" just before the script's parameter declaration. 
	

Another write-* command that you can use which has it's own special pipeline (and doesn't impact the main output) is:

write-warning "line2808"

However this isn't as good as write-verbose because because you can enable/disable write-warning outputs like you do with write-verbose.  
Also the write-warning command is designed for showing error messages to the end-user, rather than the scripts developer. 

However write-warning is useful if your script fails and terminates early, then you can use 



You can configure scripts to accept input from the pipeline. This is done by applying a config setting to one of the script's or function's parameters:

Get-Content C:\testlist.txt		# Here we have a list of values to be piped in. 
function machines {
    param(
			[parameter(mandatory=$true,valuefrompipeline=$true)]
			[string[]]$computername 
    )
    BEGIN {"start of process"}
    PROCESS {
        foreach ($computer in $computername) {
            "We have a machine called: $computer"
        }    
    }
    END {"end of process"}     
}
	
machines -computername 'one','two','three'		# Here we are feeding a parameter with an array. 

'item1','item2','item3'  | machines				# Here we are piping in an array into the function. 

Get-Content C:\testlist.txt | machines			# Here we are piping in a document's content. 



Sometimes you will want to restrict the size of the array that can be accepted, e.g. a script might take a long time to process an array containing a million items. Here is how this is done:

 

 param(
			[parameter(mandatory=$true,valuefrompipeline=$true)]
			[ValidateCount(1,10)]									# This only allows arrays containing 1 to 10 items. 
			[string[]]$computername 
    )

However this only works if you pass in the data as parameters rather than piping it in. Need to investigate how to restrict piping in as well. 

There are a lot more of these kinds of configurations, and you can find them here:

help about_functions_advanced_parameters


You can also set up a on-off style "switch parameter". It is quite simple to do:

 param(
			[switch]$RunCleanUpScript 
    )

By default the value of this parameter is $false, unless the user runs the script with -RunCleanUpScript, in which case it will switch it on. 

  
If there is a mandatory parameter that a user is meant to define when running a script, then a popup prompt window will appear if they fail to declare it when starting the script. User can then enter the values into this prompt. You can customise the help message that appears with the 
popup message to help the user understand what information that they have to provide:


function machines {
    param(
			[parameter( mandatory=$true,
                        valuefrompipeline=$true,
                        HelpMessage="Please enter pc name or IP address")]		# This is the help message
            [ValidateCount(1,3)]
			[string[]]$computername 
    )
    
    BEGIN {"start of process"}
    
    PROCESS {
        foreach ($computer in $computername) {
            "We have a machine called: $computer"
        }    
    }
    END {"end of process"}     
}  
machine -computername 'a','b','c'
machines				# Prompt window will pop up here, because mandatory parameter "computername" has not been declared here.