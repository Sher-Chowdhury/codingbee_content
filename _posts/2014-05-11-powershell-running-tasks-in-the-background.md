---
ID: 68
post_title: 'PowerShell &#8211; Running tasks in the background'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-running-tasks-in-the-background
published: true
post_date: 2014-05-11 00:00:00
---
PowerShell lets you run commands in the background and you can then retrieve the results later.This is useful if you want to run
time consuming commands and you want to keep the terminal free. Here's how to start a background job.

[powershell]
start-job -scriptblock {get-childitem}
[/powershell]

or if you have multiple commands, you can do:

[powershell]
start-job -scriptblock {
    get-childitem
    pwd
}
[/powershell]

or if you want to run a whole script in the background, then do:

[powershell]
start-job -filepath {c:\path\to\powershell-script.ps1}
[/powershell]

The "start-job" cmdlent is designed to be self-contained so it cannot access any outside variables. If you want your script block to make use of an outside variable then you need to pass it into the start-job command using the "argumentlist" option. This approach also requires you to   makes use of the "param" construct (which is commonly used for parametizing functions and scripts) Here's an example:

[powershell]

$message1 = &quot;Hello&quot;
$message2 = &quot;Goodbye&quot;

Start-Job -ScriptBlock {
    param(
        $message1,
        $message2
    )

    &quot;First arguement is: $message1&quot;
    &quot;Second argument is: $message2&quot;

} -ArgumentList $message1,$message2

[/powershell]

Whenever you start a new job, it ends up running inside a newly generated "powershell.exe" process. You can view these new process using the task manager, or just use the get-process command.   So the more background jobs you are running, the more "powershell.exe" processes it creates.

Note, you can start "Task Manager" using the taskmgr command:

[powershell]
taskmgr
[/powershell]

This will create a job object (row). To view a list of jobs that we have started, do:

get-job

Some jobs can start child jobs, you can see them, if any, by passing the above through format-list:

get-job | format-list

To get more info about all the childjobs, you can do:

get-job | select-object -expandproperty childjobs

# the above is like taking the content out of the child property (box), but since the content is also another (get-job) object,
then it displays the child jobs as jobs rather than just strings. If you just want the child job names, then do:

get-job | select-object -expandproperty childjobs | select-object -expandproperty name

If you want to see info for the childjobs of a given job id, then simply do:

get-job -id {number} | select-object -expandproperty childjobs

Note, you can't use recieve-job command twice on the same job id. That's because the cache gets emptied on the first
run (however, you can use the "-keep" parameter to retain it in cache). Note, the "get-job" will still show the job
even if the cache is emptied. If you want to remove a "completed" job from the job list, then do:

remove-job -id {jobs id}

If the job is still running, or hanging, then you first do:

stop-job -id {jobs id}

This command might stop the job slowly, which I thinks is because it stops the process gracefully. if you want to stop it faster, then I think this might be possible by stopping the corresponding powershell.exe process, using "stop-process".

&nbsp;

Then you use the remove-job.

The output of recieve-job can be piped into other command like normal, e.g.

receive-job -id {job's id} | select-object -property name

############ Detour - Start
If you want to do start-job, and pass variables into it, as well as doing initilization (e.g. import a module), then do this:

Start-Job -InitializationScript {Import-Module -Name SSH-sessions} -ScriptBlock {
param($ServerName,$Username,$Password,$LinuxCreateTarGzCommand) # you have to include this.
New-SshSession -ComputerName $ServerName -Username $Username -Password $Password
Invoke-SshCommand -ComputerName $ServerName -Command $LinuxCreateTarGzCommand
} -ArgumentList $ServerName,$Username,$Password,$LinuxCreateTarGzCommand

Got this info from:

http://stackoverflow.com/questions/12293701/function-not-recognized-in-start-job

The above could be a solution to run code in the background in order to prevent the winform gui from freezing. Need to investigate this in more detail as I haven't got this working yet.

############ Detour - end

You can also create jobs, where the commands run on a remote machines. This can be done by:

1. Use commands that supports -computername parameter, e.g.
start-job -scriptblock {get-eventlog -logname security -computername &lt;machine1,machine2,machine3=&gt;}
2. Use get-wmiobject command, which has a builtin "-asjob" parameter as well as the "-computername" parameter. So both
are used together. e.g.:
get-wmiobject -namespace root\cimv2 -class win32_service -computername {machine1,machine2,machine3} -asjob
(However you can also ignore using -asjob, and just run the command within start-job instead). The downside is
that this method is quite slow because get-wmiobject runs on one machine at a time.
3. One way around the slow performance problem with using get-wmiobject, is to run get-wmiobject in conjunction with "invoke-command". This is made possible because
invoke-command which also accepts both -computername and -asjob parameters, e.g.:
invoke-command -computername machine1,machine2 -asjob -scriptblock {get-wmiobject -namespace root\cimv2 -class win32_service}
..or go one step further and replace -asjob with start job, effectively having a 3 level nest:
start-job -scriptblock {invoke-command -computername machine1,machine2 -scriptblock {get-wmiobject -namespace root\cimv2 -class win32_service}}

Note, we did machine1,machine2....this will mean 2 child process will be created for each job.
Note, the invoke-command alway adds an extra "pscomputername" property to the output, so to keep track of which machine each object came from.

wait-job # this is something you can put in a script to allow a job to end before moving on to the next part.
# Although not sure why this is necessary.

You can also schedule jobs like the linux equivalent of "cron" or "at". Setting up a
scheduled job is done in 2 parts:

First you create a schedule using the following 2 command:

new-jobtrigger
register-scheduledjobs

See the examples in help pages to see how they work. Looks like the best way to do is merge both commands into one
by nesting them.

&nbsp;

&nbsp;

You can allocate more cpu resource to a job, by changing the corresponding process's priority:

http://powershell.com/cs/blogs/tips/archive/2014/01/02/lowering-powershell-process-priority.aspx

http://forums.extremeoverclocking.com/showthread.php?t=350101

&nbsp;

Another way to increase performance is by using "runspaces", need to do more research on this:

http://thesurlyadmin.com/2013/02/11/multithreading-powershell-scripts/

http://learn-powershell.net/2012/05/13/using-background-runspaces-instead-of-psjobs-for-better-performance/

&nbsp;

Also check out the "start-powershell" cmdlet (which is in the pscx module). Starts a new Windows PowerShell process using PowerShell's parameter parsing engine to parse the parameters for the PowerShell executable.
This command exposes a few of the Start-Process commands it uses such as -Wait, -Credential and -WorkingDirectory. Note: If -NoNewWindow is specified, PowerShell is invoked using the call operator (&amp;) instead of with the Start-Process cmdlet.