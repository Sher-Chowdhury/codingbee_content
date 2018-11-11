---
ID: 90
post_title: 'PowerShell &#8211; Creating Powershell workflows'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-creating-powershell-workflows
published: true
post_date: 2014-05-11 00:00:00
---
Chapter 18 - Creating Powershell workflows

Note: workflows is a powershell v3+ only thing. 

When you want to create a custom command, you can do so by either:

	- creating a script to represent that command
	- creating a script that contains functions and each of these function act as a command (either by dot sourcing, or importing module)

However (in ps v3), there is a third way, and that is by creating a "workflow".


When you create a function, you use the following syntax:

function command-name {
							# The code are entered here, e.g. param section, begin/process/end section
}

However when we creat a workflow, we use this syntax: 

workflow command-name {
							# The code is entered here. 
}


For example here is the contents of ps1 script we that we created called "TestWorkFlow.ps1":

PS C:\> Get-Content .\testworkflow.ps1
workflow basic-maths {
    $a=5
    $b=7
    $c= $a * $b
    $c
}
basic-maths

Now if we run this, we get:

PS C:\> .\testworkflow.ps1
35

When you run the above command, you'll notice that there is a fraction of a second delay outputing the result (35), when compared to running the equivalent command in powershell. That's because:

	- functions are processed by the powershell engine, whereas...
	- workflows are processed by the .NET Framework's "Windows Workflow Fondataion (WF)"

As a result, workflows and functions have differing behaviours:

- Both functions and workflows run commands in the same sequence, but workflows include detailed logging/tracking of each command and include the 
  ability to retry steps (if they fail for things like intermitant network issues, maybe useful for developing pandabox/dropbox!)
- Functions do one thing at a time, whereas workflows can do the same thing multiple times (parrallel multitasking)
- Functions start/run/finishes, whereas a workflow can pause/stop/restart
- If you turn off your machine while a function is running, then that function is lost. However a workflow can potentially be recovered and 
  resumed automatically.    

As a result of these distinctions, creating a workflow is a bit like creating a service!!!

Useful link that gives more background info about workflows: http://technet.microsoft.com/en-gb/magazine/jj884462.aspx
Also checkout:
PS C:\> help about_Workflows



Here is a summary of the differences between functions and workflows:

------------------------------------------------------------------------------------------------------------------------------
                         Function 								|                         Workflow
------------------------------------------------------------------------------------------------------------------------------
	Executed by Powershell 										|  	Executed by workflow engine
																|
	Logging and Retry attempts throught complicated coding  	|	Logging and retry attempts part of the workflow engine. 
																|
	Single-action processing									|  	Supports parallelism
																|
	Runs to completion											|  	Can run/pause/restart
																|
	Data loss possible duirng network problems					|  	Data can persist during network problems. 
																|
	Full language set and syntax								|  	Limited language set and syntax
																|
	Run cmdlets													|  	Run activities



The workflow features is available in powershell in the form of a module, called "PSWorkflow". You need to first import this module so that powershell understands what workflows are:


PS C:\> Get-Module PSWorkflow
ModuleType Name                                ExportedCommands
---------- ----                                ----------------
Manifest   PSWorkflow                          {New-PSWorkflowExecutionOption, New-PSWorkflowSession, nwsn}

PS C:\> Import-module  PSWorkflow

After doing this, ps will now understand what workflows are and how to handle them. 

To transform a workflow into a command, you need to add the worflow into a modules script (psm1), i.e. :


Import-Module psworkflow		# include this anyway in case psworkflow hasn't already been imported. 
workflow get-maths {
    $a=5
    $b=7
    $c= $a * $b
    $c
}
export-ModuleMember  -function get-maths 		# This might not be needed if your module doesn't have any preference settings (e.g. setting 
												# location of the log file). But best practice is to always include this. 
												# surprising we use the "-function" parameter because "-workflow" doesn't exist. 


Now you can simply run the workflow like a normal command:

PS C:\> get-maths
35
												

As you will recall, commands and functions have common parameters (see help about_common_parameters). In the same way, workflows comes with their own set of common parameters.

												
One of the really useful parameter, is the "-asjob" switch parameter. This lets you run a workflow-based-command in the background:

PS C:\> get-maths -AsJob

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
35     Job35           PSWorkflowJob   NotStarted    True            localhost            get-maths                

You can then use the -job commandlets to manage this job. e.g:

PS C:\> get-job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
19     Job19           PSWorkflowJob   Completed     True            localhost            get-maths                
35     Job35           PSWorkflowJob   Completed     True           localhost            get-maths                

PS C:\> Receive-Job -id 35
35

There is also "suspend-job" and "resume-job" to pausing and resuming the workflow. 
 

The "-asjob" is one of the most useful workflow common parameter, but there other useful ones too:

	PSComputername - A list of computers to perform the workflow on. Note, some commands cannot be remoted, e.g. measure-object, see page
					 187 for a complete list of commands that can't be remoted with this parameter. This is mainly for performance reasons,
					 however you can force workflow to run these commands remotely using the "InlineScript{}" block, which is covered later. 
	PSParameterCollection - A list of hash tables that specify different parameter values for for each target machine, so each machine can be 
							treated differently. 
	PSCredential - The username/password to be used to execute the workflow. 
	PSPersist 	- Force the workflow to save (checkpoint) the workflow data and start after executing each step. A bit like log taking. 
		

For a complete list, check out:

help about_WorkflowCommonParameters



Workflows works on the concept of "activities". Each command that runs within a workflow is a standalone "activity". In order for workflows pause/start feature to work, each command has to assume it is running in a completely fresh, brand new environment. Hence, variables created in one command, can't be used in the next (However there are a few exceptions to this rule, e.g. simple variable that holds numbers). 

However you can use the following construct to tell workflows to run a code block as a single activity:

Import-Module psworkflow
workflow get-maths {
    $a=5
    $b=7
    $c= $a * $b						# Simple variables like this will work fine. 
    $c
    #$object = get-service			# this doesn't work becuase outside of inlinescript block.   
    #$object						# Therefore has been commented out. 
	InlineScript {					
		$object = get-service		
        $object							# This works fine. 
	} 
}
export-ModuleMember  -function get-maths


You can suspend (pause) a workflow, by placing a pause command (Suspend-Workflow) inside the workflow itself:


Import-Module psworkflow
workflow get-maths {
    $a=5
    $b=7
    $c= $a * $b
    $c
    Suspend-Workflow					# Here is the pause command. 
	InlineScript {
		$object = get-service
        $object
	} 
}
export-ModuleMember  -function get-maths

The above will cause the job to go into a "job" as soon as it reaches the pause command:

PS C:\> get-maths
35

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
59     Job59           PSWorkflowJob   Suspended     True            localhost            get-maths                


Notice, that the state is "suspended", i.e. that it is paused. To resume, simply do:

resume-job -id 59		# you then have to wait awhile and then do "receive-job -id 59",  in order to get the output.


A while back we covered the invoke-command. One of the things this command lets you do is this:

Invoke-Command -ScriptBlock {block-of-code} -computername {machine1,machine2...etc}

One of the cool thing about invoke-command is that it will run the scriptblock simultaneously across several machines at the same time. This is a 
great way to do things efficiently. However the lines of code inside the scriptblock will be performed sequentially (unless maybe you make use of workflows within the scriptblock). 

In workflows you can control the order in which lines of codes are executed using the following parallel/sequence construct:


Import-Module psworkflow
workflow get-maths {
	parallel {
		
		command1
		command2
		
		sequence{
			commandA
			commandB
		}
	
	
	}
}
export-ModuleMember  -function get-maths
 

In this scenario, the above 4 commands can run in the following possible orders. 


command1	|	command2	|	command1	|	command2	|	commandA	|	commandA
command2	|	command1	|	commandA	|	commandA	|	commandB	|	commandB	
commandA	|	commandA	|	commandB	|	commandB	|	command1	|	command2	
commandB	|	commandB	|	command2	|	command1	|	command2	|	command1	


Notice that commandB always comes after CommandA, and the commandA-B block is competing equally with commands 1 and 2 to be executed first (since the sequence block itself is in the parallel block).

Of the 6 possible orders, anyone is possible. The only thing that determines the order is the amount of time each of the above 4 commands takes to complete. 

The above is useful if you have a set of commands to run and don't care about the order that they are run in. 

Here is another kind of parrellel syntax that you can use in workflows:

workflow test-workflow {
	Foreach -parrallel ($computer in $computername) {
		Do-something -computername $computer
	}
}

This approach does pretty much the same thing as the invoke-command with the "-computername" parameter enabled. 


As mentioned earlier, anything you run in a workflow gets passed to a .net engine which then translates each powershell command into the equivalent .net "activity". However there are some ps commands that don't have a .net equivalent, in these case these commands will fail. This is especially the case for any commands that are part of a module. With module based commands, one way to overcome this is to do:

inlinescript{
	import-module {module's name}
	module's-command
} 

You will often find yourself using "inclinescript" quite regularly when developing workflows.   

page 190 - gives a list of things you can do in functions but can't do in workflows. 

page 191 - gives a list of all built-in commands that don't work in woflows.