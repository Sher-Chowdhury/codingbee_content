---
ID: 172
post_title: 'Ruby &#8211; Use backticks and system to run Bash or Powershell commands'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-use-backticks-and-system-to-run-bash-or-powershell-commands
published: true
post_date: 2014-11-12 00:00:00
---
In ruby you can embed system commands (e.g. bash or Powershell) inside your rb scripts. 

The commands that are available depends on whether you are working with a Windows or Linux Operating System.  

<h2>Windows Operating System</h2>
Here's a script:


<pre>
irb(main):030:0> `time /t`
=> "12:10\n"                   # Notice that the new line (\n) formatting doesn't get interpreted. 

# There is also the %x(...) which is an alternative to the backtick notation:
irb(main):002:0> %x(time /t)   
=> "14:00\n"

</pre>

We also have "system" which is slightly different to backtick, in the sense that it returns a boolean ("true" if command as 0-exit-status, and "nil" for all other exit status). However this command also outputs the actual commands output to the standard output. 
<pre>
irb(main):031:0> system("time /t")
12:11
=> true                  # system also outputs a boolean on whether it was succesful. 

# now let's fail on purpose:           
irb(main):032:0> system("timetttt /t")
=> nil

# Now let's capture the output:
irb(main):004:0> result = system("time /t")
14:06
=> true
irb(main):005:0> puts result        
true                   # notice that the result variable only holds the boolean value 
                       # and not the output itself.  
=> nil

</pre>

The "time /t" comand is actually a <a href="http://ss64.com/nt/">cmd command</a>. By default anything passed into backticks/system is assumed to be a windows cmd command. This means a command like "ls" won't work, instead you have to use the cmd equivalent, whcih is "dir". The same is true if you try to run any powershell commands, e.g.:

<pre>
irb(main):005:0> `get-childitem`
Errno::ENOENT: No such file or directory - get-childitem
        from (irb):5:in ``'
        from (irb):5
        from C:/Program Files (x86)/Ruby/Ruby193/bin/irb:12:in `<main>'
irb(main):006:0> system("get-childitem")
=> nil
irb(main):007:0>
</pre>

However you can run powershell comands from inside ruby, by prepending your command with "powershell", like this:

<pre>
irb(main):014:0> `powershell get-childitem`
=> "\n\n    Directory: C:\\\n\n\nMode                LastWriteTime     Length Name
                                \n----                -------------     ------ ----
                                 \nd----        24/10/2014     11:51            DeployTasks
                                  \nd----        23/04/2014     11:29            GeoWorkspaces
                                   \nd----        15/08/2014     14:50            HashiCorp
                                    \nd----        08/03/2014     11:21            Intel
                                     \nd----        27/06/2014     12:10            IWTemp
                                      \nd----        30/05/2014     12:14            jar
                                       \nd----        16/03/2014     14:52            oracle
                                        \nd----        14/07/2009     04:20            PerfLogs
                                         \nd----        30/05/2014     09:23            Portable-winscp550
                                          \nd----        16/03/2014     12:54            Powershell
                                           \nd-r--        18/07/2014     12:29            Program Files
                                            \nd-r--        12/11/2014     10:32            Program Files (x86)
                                             \nd----        30/05/2014     12:14            PuTTY

irb(main):015:0> system("powershell get-childitem")


    Directory: C:\


Mode                LastWriteTime     Length Name
----                -------------     ------ ----
d----        24/10/2014     11:51            DeployTasks
d----        23/04/2014     11:29            GeoWorkspaces
d----        15/08/2014     14:50            HashiCorp
d----        08/03/2014     11:21            Intel
d----        27/06/2014     12:10            IWTemp
d----        30/05/2014     12:14            jar
d----        16/03/2014     14:52            oracle
d----        14/07/2009     04:20            PerfLogs
d----        30/05/2014     09:23            Portable-winscp550
d----        16/03/2014     12:54            Powershell
d-r--        18/07/2014     12:29            Program Files
d-r--        12/11/2014     10:32            Program Files (x86)
d----        30/05/2014     12:14            PuTTY



=> true

irb(main):016:0> system("powershell ls")

   Directory: C:\


Mode                LastWriteTime     Length Name
----                -------------     ------ ----
d----        24/10/2014     11:51            DeployTasks
d----        23/04/2014     11:29            GeoWorkspaces
d----        15/08/2014     14:50            HashiCorp
d----        08/03/2014     11:21            Intel
d----        27/06/2014     12:10            IWTemp
d----        30/05/2014     12:14            jar
d----        16/03/2014     14:52            oracle
d----        14/07/2009     04:20            PerfLogs
d----        30/05/2014     09:23            Portable-winscp550
d----        16/03/2014     12:54            Powershell
d-r--        18/07/2014     12:29            Program Files
d-r--        12/11/2014     10:32            Program Files (x86)
d----        30/05/2014     12:14            PuTTY


=> true
irb(main):017:0>

</pre>

You can use this approach to also run powershell scripts, i.e.:

<pre>
PS C:\Temp> cat .\powershell_script.ps1
echo "Hello CodingBee!"
PS C:\Temp> irb
irb(main):001:0> system('powershell -File c:\temp\powershell_script.ps1')  # Note, is single quotes.
Hello CodingBee!
=> true
irb(main):002:0>
</pre>



<h2>Linux</h2> 

In Linux, you can use the same approach as above. Essentially if a command runs on the bash command line, then it should work from inside ruby. 

Here is an example of <a href="http://blog.bigbinary.com/2012/10/18/backtick-system-exec-in-ruby.html">executing bash commands from inside ruby</a> 























By default, the backtick and system approach both outputs the following: