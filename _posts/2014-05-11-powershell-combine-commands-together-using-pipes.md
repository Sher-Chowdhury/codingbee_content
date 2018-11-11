---
ID: 60
post_title: 'PowerShell &#8211; Combine commands together using pipes'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-combine-commands-together-using-pipes
published: true
post_date: 2014-05-11 00:00:00
---
compare-object # similar to the diff command. but has a lot more feature for comparing tabular data. Hence can be used to compare
# what is and is-not installed as well as versions for ordnance survey.

<pre>## Redirection
Get-ChildItem | Out-File testfile.txt # This is the same as "ls -l=> testfile.txt"

Get-ChildItem | Out-File testfile.txt -append # This is the same as "ls -l =>=> testfile.txt"
</pre>

If you don't specify the out-file command, then powershell will always assume the default which is "out-default", which in turn sends it to "out-host". "Out-host" is another name for the command line terminal.

<pre>measure-object # equivalent to "wc -l"
</pre>


<pre>Get-ChildItem | Out-null # same as: ls -l =>/dev/null
</pre>

Note, if you want to discard error messages then, at the end of the command, add "2=>&amp;1 | Out-Null". This is equivalent to Linux's "2=>&amp;1 1=>/dev/null". For example:

<pre>Remove-Item C:\WINDOWS\system32\WindowsPowerShell\v1.0\Modules\pscx -Recurse 2=>&amp;1 | Out-Null
</pre>
In the above example, we are deleting a folder, which might, or might not exist in the first place.

Note you can also do:

<pre>Remove-Item C:\WINDOWS\system32\WindowsPowerShell\v1.0\Modules\pscx -Recurse 2=>Out-Null
</pre>

<pre>get-content .\testfile.txt | ConvertTo-Html | Out-File homepage.html # This takes the content of a file (which in this case
# contains ls -l data) and outputs as html.
</pre>
there are other convert-to... commands. E.g. you can do:

get-service | ConvertTo-Csv # this outputs to the terminal

get-service | ConvertTo-Csv | out-file testfile.csv # This creates a csv file, a shorter way to write this is like this:

get-service | Export-Csv testfile.csv

Commands that are from the same family are better at communicating with each other:

Get-Process -name notepad++ | Stop-Process

Here we are closing the notepad++ app. and the 2 "process" commands are working well together. The linux equivalent is a 2 step command:

ps -aux | grep -v grep | grep 'notepad++' # to identify the process id. Then do:
kill 9 pid

You can fit this into a single command using awk, but takes longer to write that command. You should always be careful when using commands like stop-process and stop-service.

Windows has an "are you sure" system. You can see the current-level of the are-you-sure system like this:

PS C:\Users\mir\Desktop=> $ConfirmPreference
High

You can change the threshold this like this:
$ConfirmPreference="low"
PS C:\Users\mir\Desktop=> $ConfirmPreference
Low

For more info, see:
help about_Preference_Variables

You can manually make a command ask the are-you-sure command like this:

Get-Process -name notepad++ | Stop-Process -Confirm

This will open a are-you-sure popup window.

There is also the what-if command:

Get-Process -name notepad++ | StopProcess -whatif
What-if: Performing operation "StopProcess"
on Target "notepad++ (7596)".

This option shows what would happen if you run the command, without actually running it.