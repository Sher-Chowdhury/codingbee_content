---
ID: 335
post_title: 'RHCSA &#8211; Standard Outputs, Inputs, and Errors'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-standard-outputs-inputs-and-errors
published: true
post_date: 2015-04-03 00:00:00
---
<h2>Overview of standard Outputs/Inputs/Errors</h2>

By the end of this article you should be able to answer the following questions:

[expand title="How many types of outputs can a command give out, and what are they called?"  alt=" "]
Two types, They are called, "standard output" and "standard error". 
[/expand]

<hr/>


<h2>Standard Outputs and Standard Errors</h2>

In Linux, a command can return 2 types of outputs, they are called <strong>Standard Outputs</strong> and <strong>Standard Errors</strong>. Redirection gives you a further level of management control when redirecting Standard Outputs and Standard Errors.   


The <strong>standard output</strong> is also known as <strong>Type 1</strong>) outputs. Similarly <strong>standard error</strong> outputs is known as <strong>Type 2</strong>. Standard errors, are more commonly recognized as error messages. By default all outputs, regardless of the type, are outputted to the screen. In most cases it is quite easy to identify the output type e.g.: 

<pre>
$ ls -l testfile.txt
ls: cannot access testfile.txt: No such file or directory
</pre>

The above shows a "Standard Error" type message. A more definitive way to determine what type output a command outputted is by view a command's "exit status", which is stored in the special parameter, <code>$?</code>:

<pre>
$ ls -l testfile.txt
ls: cannot access testfile.txt: No such file or directory
$ echo $?
2
</pre>


If the return value is "0", then the last command ran gave out type 1 messages. Otherwise it is a type 2 message. 

<pre>
$ touch testfile.txt
$ ls -l testfile.txt
-rw-r--r--. 1 root root 0 Oct 19 20:38 testfile.txt
$ echo $?
0
</pre>

Note: All the available special parameters are documented in <code>man bash</code>, just search for "Special Parameters"


<h2>Redirection</h2>
In some cases, you might want to capture type 1 messages messages into a file. This is acheived by  redirecting the standard output outputs to a file:

<pre>
$ ls -l testfile.txt > info.log
$ cat info.log
-rw-r--r--. 1 root root 0 Oct 19 20:38 testfile.txt
</pre> 

However this doesn't automatically redirect standard errors (type 2 outputs). That's because the above command is an implicit version of the following command:

<pre>$ ls -l testfile 1>>info.log</pre>

The "1>>info.log" basically means <italic>only redirect type1 outputs to the file called info.log<italic>. The <code>>></code> means append to this file, whereas a single <code>></code> means set the content of this file. In both cases it will create the file if it doesn't already exist.  

Therefore type 2 outputs are outputed to the screen, e.g.: 

<pre>
$ ls -l i-dont-exits.txt > info.log
ls: cannot access i-dont-exits.txt: No such file or directory
$ echo $?
2
$ cat info.log
$
</pre>



If you want to also redirect type2 outputs to the same place as type 1, then do:

<pre>
$ ls -l testfile123 >info.log 2>&1
</pre>

Here, "2>&1" means <italic>redirect type 2 outputs to where ever type1 outputs are being sent to<italic>

To summarise, There are actually several ways you can write the above command:

<pre>
$ {command} >>info.log 2>&1
$ {command} 1>>info.log 2>>info.log
$ {command} 2>>info.log 1>&2
$ {command} 2>>info.log >&2
</pre>

You can also display type1 on screen and just keep a log of type2, like this:

<pre>
$ {command} 2>>info.log
</pre>


Another popular option is to store type1 and type2 outputs in seperate files:

<pre>
$ {command} 1>>info.log 2>>error.log
</pre>


<h2>Discarding Outputs</h2>
Sometimes you might want to ignore outputs and don't display/store them anywhere. To do this, you need to redirect the output to a special file, <code>/dev/null</code>. For example if you want to discard any type2 outputs, but display type1 on screen, then do:

<code>
$ {command} 2>>/dev/null
</code>

<code>/dev/null</code> is a special file that's solely used for discarding outputs. It is a bit like Microsoft window's recycle bin. Some people also refer to it as the "black hole". Once you have sent an output to the <code>/dev/null</code>, thats it, you wont be able to recover it again.  

Discarding type2 outputs is not a good idea, since they can be informative and can help you resolve issues. However sometimes it can be useful, such as when <l>using the find command<l>

<h2>Spacing doesnt matter</h2> 
In the above examples we use a file called "info.log". But it doesn't really matter what you call the files whatever you like. e.g. you can call them log.txt, or just info
You can also specify the whole path of the file, to make sure the files are located where you want them. So instead of "info.log", you can have them as "/tmp/info.log"
Anywhere that you see a redirection which is not preceded with a digit, e.g. " >info.log", then you should read this as the "1" is implied. Hence " >info.log" can also be written as " 1>info.log". People often leave out the "1" because it saves them from extra typing. 






<h2>Useful links</h2>
http://www.cyberciti.biz/faq/redirecting-stderr-to-stdout/
http://stackoverflow.com/questions/2342826/how-to-pipe-stderr-and-not-stdout 







By default all output messages (which is 1 and 2) are sent to the standard output which is the command prompt screen. However this can be edited. Here is an example:

find / -type f -name “*.ora” 2=>/dev/null 1=>&2

This means:

2=>/dev/null   :  any error output messages is ignored

1=>&2   :          any standard (success) output gets redirected to wherever standard error is sent to.

The following has the same effect (i.e. nothing is outputted):

find / -type f -name “*.ora” 2=>/dev/null 1=>/dev/null

You can also send error and standard info messages to different files. for example:

find / -type f -name “*.ora” 2=>error.log 1=>info.log

or

find / -type f -name “*.ora” 1=>info.log 2=>error.log

or you could leave out the “1”

find / -type f -name “*.ora” =>info.log 2=>error.log

This creates two files in the current directory and sends and saves error messages in one and info messages in the other.

Here is another example:

find / -type f -name “*.ora” =>log

This will save all success messages to the log whereas all error messages (e.g. permission denied messages) get displayed on the screen. so if you want to also save error messages to the log and not show them on screen, then you can do:

find / -type f -name “*.ora” =>log 2=>&1

or a shorthand way to write this is:

find / -type f -name “*.ora” &=>log

or

find / -type f -name “*.ora” =>log 2=>log

The above actually doesn’t work because error message overwrites success messages. The following also doesn’t work for other reasons:

find / -type f -name “*.ora” =>log 2=>=>log

The ordering of the redirection is also important, here is an example:

<pre>$ find / -type f -name “*.ora” =>/dev/null 2=>&1 | wc -l

0
</pre>

Here, the first redirection is straight to null which is why all error messages are straight away ignored.



<pre>$ find / -type f -name “*.ora” 2=>&1 =>/dev/null | wc -l
160
</pre>

The second command outputs error messages to screen before the redirection to null takes place.

Tip: In shell scripts, near to the top of the shell script, you can add:


<pre>
exec 1>> /path/to/logfile.txt 2>&1
</pre>

This means that from this point forward, all of the scripts output will get redirected to the log file. 

<h2>
See also:</h2> 
http://www.cyberciti.biz/faq/redirecting-stderr-to-stdout/

You can also create a third file descriptor, which you can use to swap round how error and standard messages are treated, see:

http://stackoverflow.com/questions/2342826/how-to-pipe-stderr-and-not-stdout