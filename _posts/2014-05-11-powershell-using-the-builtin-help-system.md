---
ID: 57
post_title: 'PowerShell &#8211; Using the builtin help system'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-using-the-builtin-help-system
published: true
post_date: 2014-05-11 00:00:00
---
<h2>Accessing the built-in help</h2>
Powershell comes with a builtin help manual, which is really useful and is something you will rely on heavily.

There are hundreds of commands at your disposal, you can view a whole list of them by running the get-command command:


<pre>PS C:\Windows\system32=> Get-Command 

CommandType     Name                                               ModuleName
-----------     ----                                               ----------
Alias           Begin-WebCommitDelay                               WebAdministration
Alias           End-WebCommitDelay                                 WebAdministration
Alias           Resize-Bitmap                                      Pscx
Function        A:
Function        Add-DirectoryLength                                Pscx
.
.
.
....etc
</pre>

You'll notice there are different types of commands, functions, aliases, and cmdlets. However the majority of them are "cmdlets" and Cmdlets are what we traditionally recognize as normal commands. Powershell uses a "verb-noun" structure for it's cmdlets. E.g. get-service, stop-process, and etc.

You can view powershell's built-in help guide for a given cmdlet using the get-help cmdlet. For example if you want to look up the help info for get-service command then do:


<pre>PS C:\Windows\system32=> help Get-Service

NAME
    Get-Service

SYNOPSIS
    Gets the services on a local or remote computer.
.
.
.
...etc</pre>


However, this doesn't give the full info. You need to use the "full" option to get all the info:


<pre>get-help -name get-service -full</pre>


A small problem with using get-help is that it usually shows the very bottom of the help page when you run this cmdlet, which means that you often have to scroll to the top every time you run this command, which can be annoying. One way to avoid this is by using the "help" instead of get-help:


<pre>get-help get-service</pre>


This only works in the powershell console, and not the powershell ise. If you are using the Powershell ISE, then another option is to use on the following get-help options:


<pre>get-help get-service -showWindows   # This displays the help info in a pop-up window
get-help get-service -online        # This displays the help info in web browser</pre>


"help" is actually a function (hence it doesn't follow the verb-noun structure), and in this case, it acts as a wrapper for the get-help cmdlet, it does all the same things as get-help, and the only difference is that default's to the top of the page rather than the bottom.

An alternative to using "help" is to use:


<pre>man get-service</pre>


"man" is actually just another name for the "help" function. In other word's "man" is an alias for "help". A lot of people use man, because man is the main linux command for accessing the built-in help on linux machines.

To summarise, "man" is an alias of the "help" function which in turn is a wrapper for the get-help command, which you can see here:



<pre>
PS C:\Windows\system32=> Get-command -Name man

CommandType     Name                                               ModuleName
-----------     ----                                               ----------
Alias           man -=> help                                                           

PS C:\Windows\system32=> Get-command -Name help

CommandType     Name                                               ModuleName
-----------     ----                                               ----------
Function        help                                                                  

PS C:\Windows\system32=> Get-command -Name Get-Help

CommandType     Name                                               ModuleName
-----------     ----                                               ----------
Cmdlet          Get-Help                                           Microsoft.PowerS...
</pre>

If you want to get a list of all the help pages available, then do:


<pre>
Get-Help *  # Lists all help topics that are available.
</pre>


A large number of the help pages are not tied to particular cmdlet, but covers more general information and concepts in PowerShell, e.g. try:


<pre>PS C:\Users\Mir=> help about_aliases</pre>


<h2>Understanding the help pages</h2>
The builtin-help has a lot of valuable information, and it's important to understand how to read the help pages to make the most it.

While not always true, a cmdlet's (full) help page usually contains several sections:
<ol>
	<li>Synopsis   - a one line description of a cmdlet</li>
	<li>Syntax - explain how to write the command (more on this later).</li>
	<li>Description - describes what the command does.</li>
	<li>Parameters - Gives info about the available cmdlet parameters.</li>
	<li>Inputs - Describes what can be piped into the command</li>
	<li>Outputs - Describes what type of object is outputted.</li>
	<li>Notes - Gives extra info about the command, e.g. if it has any aliases.</li>
	<li>Examples - Gives examples on how to use this command.</li>
	<li>RelatedLinks - Gives info about other related commands.</li>
</ol>
<h3>The "Syntax" section</h3>
At first sight the syntax section looks really complicated and scary. However it gets a lot easier once you understand how to read it.

To understand this, let's look at the help page for the move-item cmdlet:

<pre>
PS C:\Windows\system32=> Get-Help Move-Item -full
</pre>


The move-item cmdlet is used for moving files/folders (Note, if you want to copy files/folders then you need to use the copy-item cmdlet instead).

The move-item, syntax section has 2 entries:

<a href="http://codingbee.net/wp-content/uploads/2014/06/move-item-syntax.png"><img class="alignnone size-full wp-image-998" src="http://codingbee.net/wp-content/uploads/2014/06/move-item-syntax.png" alt="" width="710" height="130" /></a>

Let's ignore the second entry for now, and just look at the first entry.

As you can see the move item, has quite a lot of options, e.g. -Path, -Destination....etc. The help page's Parameter section describes what these parameters are along with additional. For the time being let's take look at the -Path and -Destination:

<a href="http://codingbee.net/wp-content/uploads/2014/06/RpT9J3B.png"><img class="alignnone size-full wp-image-999" src="http://codingbee.net/wp-content/uploads/2014/06/RpT9J3B.png" alt="" width="676" height="127" /></a>

&nbsp;

<a href="http://codingbee.net/wp-content/uploads/2014/06/hp1bj9d.png"><img class="alignnone size-full wp-image-1000" src="http://codingbee.net/wp-content/uploads/2014/06/hp1bj9d.png" alt="" width="710" height="169" /></a>

&nbsp;

When you want to move a file from one location to another, then there 2 things you have to tell the move-item command, where the file is currently located (by passing a value into the   "-Path" parameter) and where to move it to (by passing a value into the   "-Destination" parameter).

For example:

[powershell]
PS C:\Users\Public=&gt; Move-Item -Path C:\Users\Public\testfile.txt -Destination C:\Temp
[/powershell]

Now lets look at the syntax info again:

<a href="http://codingbee.net/wp-content/uploads/2014/06/move-item-syntax.png"><img class="alignnone size-full wp-image-998" src="http://codingbee.net/wp-content/uploads/2014/06/move-item-syntax.png" alt="" width="710" height="130" /></a>

The info for the first parameter is shown as:

[-Path] &lt;string[]=>

The "&lt;string[]=>" means that the Path can accept a value in the form of a string. the empty [] means that the path can also accept a string of arrays.   This means that move-item can pickup multiple items and mover them to the same location:

[powershell]
PS C:\Users\Public&amp;gt; Move-Item -Path C:\Users\Public\testfile.txt,C:\Users\Mir\Desktop\testfile2.txt -Destination C:\Temp
[/powershell]

The square brackets around -Path, i.e "[-Path]", means that, for your convenience, you can omit typing "-Path" into your command, and powershell will assume you meant "-path" instead. That is,   you can rewrite:

[powershell]
PS C:\Users\Public=&gt; Move-Item -Path C:\Users\Public\testfile.txt -Destination C:\Temp
[/powershell]

as:

[powershell]

PS C:\Users\Public=&gt; Move-Item C:\Users\Public\testfile.txt -Destination C:\Temp

[/powershell]

This means that you don't have to do as much typing and you are being more implicit than explicit, however it also makes it a little harder to understand what your command is doing. That's why I prefer writing my commands in the long-hand form.

&nbsp;

Note, this shorthand system only works if you specify the short-handed parameter values in the same order as shown in the syntax. I.e. the "-path" parameter is specified first, so this means that the following will fail:
[powershell]
PS C:\Users\Public&amp;gt; Move-Item -Destination C:\Temp C:\Users\Mir\Desktop\testfile2.txt
[/powershell]
The above failed because we specified the -path parameter's value as the second value in the string (destination's value being first). To fix this, we can either rearrange the ordering of the parameters declared, by making the -path parameter's value first, or include the "-path" into the command, or do both.

For this reason, any parameter, where you don't have to explicitly specify the parameter's name in the command, is referred to as a "positional parameter", and it must be declared in the 1st position, when declaring implicitly. You can confirm the position by looking at the parameter section of the help page again:

<a href="http://codingbee.net/wp-content/uploads/2014/06/p184BB4.png"><img class="alignnone size-full wp-image-1001" src="http://codingbee.net/wp-content/uploads/2014/06/p184BB4.png" alt="" width="677" height="124" /></a>

In general, anything that is encased in square brackets, indicates that it is optional. The only exception to this is when we have empty square bracket, [], which denotes arrays.

Notice that while [-path] is optional, it's corresponding value &lt;string[]=> is not encased in square brackets, that means that you have to declare the -path parameter's value every time, i.e. it is mandatory. That's why parameters like this are referred to as a "mandatory positional parameters". Again you can confirm this in the parameter section of the help page:

<a href="http://codingbee.net/wp-content/uploads/2014/06/TyENxcR.png"><img class="alignnone size-full wp-image-1002" src="http://codingbee.net/wp-content/uploads/2014/06/TyENxcR.png" alt="" width="677" height="124" /></a>

Now let's take a look at syntax info for the -destination parameter:

[[-Destination] &lt;String=>]

here's you'll see that note only is the this parameter also a positional parameter (since "destination" is encased in square brackets), but the outer bracket indicates that the whole thing is optional, i.e. this is a "optional positional parameter". How is this possible? How will move-item know where to send the file, if you don't provide a destination path? That's because the destination parameter has a default value which is the current directory. Once again, you can confirm this in the parameter section of the help page:

<a href="http://codingbee.net/wp-content/uploads/2014/06/u6CWikE.png"><img class="alignnone size-full wp-image-1003" src="http://codingbee.net/wp-content/uploads/2014/06/u6CWikE.png" alt="" width="690" height="167" /></a>

&nbsp;

based on all this information, you can rewrite the following long-form command:
[powershell]
PS C:\Temp&amp;gt; Move-Item -Path C:\Users\Public\testfile.txt -Destination C:\Temp
[/powershell]
....into a more shorthand form by implicitly declaring the parameter values:
[powershell]
PS C:\Temp&amp;gt; Move-Item C:\Users\Public\testfile.txt C:\Temp
[/powershell]
but since we are already in the destination folder, we can write this in even more shorthand mode:
[powershell]
PS C:\Temp&amp;gt; Move-Item C:\Users\Public\testfile.txt
[/powershell]
If you check the notes section of the move-item help page, you'll find that move-item has an alias "mv", this mean's that you can write this command in an even more shorthand mode:
[powershell]
PS C:\Temp&amp;gt;   mv C:\Users\Public\testfile.txt
[/powershell]
Earlier you will have noticed that the move-item command has 2 syntax. That's because move-item actually has 2 operational modes. If you closely compare the 2 entries, you'll notice a subtle difference:

<a href="http://codingbee.net/wp-content/uploads/2014/06/XSiABAL.png"><img class="alignnone size-full wp-image-1005" src="http://codingbee.net/wp-content/uploads/2014/06/XSiABAL.png" alt="" width="715" height="123" /></a>

Here this means, that if you don't declare the path parameter value, but explicitly declare the LiterallyPath parameter, then you are actually activating the second mode. Notice that -Path and -LiteralPath are both mandatory, but only mandatory in their respective modes, and the one you declare will dictate which mode move-item operates in. Hence you can't declare both of them at the same time, because that would be meaningless and will result in an error.

&nbsp;

Another good example of a command that has multiple operational modes, is the command get-help command itselft:

<a href="http://codingbee.net/wp-content/uploads/2014/06/q8YaIQk.png"><img class="alignnone size-full wp-image-1006" src="http://codingbee.net/wp-content/uploads/2014/06/q8YaIQk.png" alt="" width="716" height="435" /></a>

Here it's more obvious why there are more modes. For example you can't view the "-full" help page, and also only view the "-examples" help age.

These "operational mode" controlling parameters are quite hard to identify by reading the help pages, you have to look really closely to spot which parameters are controlling the operational mode, luckily there is a way to see them more clearly with the help of show-command, e.g. :

[powershell]
show-command -Name Get-Help
[/powershell]

This opens up a gui window where the tabs along the top shows the various operation mode of the given command. 

show-command is a tool that you can use to write your commands via a user-friendly gui interface, which can be handy if you are struggling to write your commands.  

Parameter values for a given parameter can take many forms (i.e. datatypes). E.g they can be a boolean, string, integer, or a custom object. In fact, in powershell,   all data types are a kind of an object. We will cover more about "objects" in later lesson.

Tip: The PowerShell console can get a little cluttered when you use it for viewing help pages. In such instances you can clear your console using the clear-host cmdlet.