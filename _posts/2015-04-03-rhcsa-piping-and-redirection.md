---
ID: 336
post_title: 'RHCSA &#8211; Piping and Redirection'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-piping-and-redirection
published: true
post_date: 2015-04-03 00:00:00
---
skip this

<h2>Piping and Redirection
</h2>
Piping and redirection are techniques that gives the linux command line an incredible amount of power and versatility. Piping and Redirection works by essentially controlling the flow of standard inputs and standard outputs. 


<h2>Piping</h2>

Piping is a technique that lets you use Linux commands as building blocks to build your own custom commands. To understand how piping works, you need to see it in action. Lets assume we have a list of files:


<pre>
$ ls -l
total 0
-rw-r--r--. 1 root root 0 Oct 20 19:22 file1
-rw-r--r--. 1 root root 0 Oct 20 19:22 file2
-rw-r--r--. 1 root root 0 Oct 20 19:22 file3
drwxr-xr-x. 2 root root 6 Oct 20 19:22 folder1
drwxr-xr-x. 2 root root 6 Oct 20 19:22 folder2
drwxr-xr-x. 2 root root 6 Oct 20 19:22 folder3
</pre>
 

Now lets say you want to only list files and no directories. Then one way to do this is by using piping:

<pre>
$ ls -l | grep "^-"
-rw-r--r--. 1 root root 0 Oct 20 19:22 file1
-rw-r--r--. 1 root root 0 Oct 20 19:22 file2
-rw-r--r--. 1 root root 0 Oct 20 19:22 file3
</pre>



Here we used the pipe character, "|", to redirect the output of of "ls -l", into the grep command.
The way it works is that The first part "ls -l" generates the output that would normally would show up on the screen (aka <l>standard output<l>). However due to the pipe, the standard output is instead redirected (i.e. piped) as the input (aka <l>standard input<l>) for the next command (grep). The "^-" instructs grep to only display lines that begins with "-". This means that it filters out any lines starting with "d" (i.e. directories). The grep's standard output is then displayed on the screen.

If instead you want to count the number of files there are (rather than a list of the files), then you can do this by adding another pipe:

<pre>
$ ls -l | grep "^-" | wc -l
3
</pre>

Here, the standard output from the grep command is redirected into the (w)ord(c)ount command. The -l option instructs wc to only do a line count. Hence the resulting output is equal to the number of files. 

This is just a simple example of how powerful piping can be. 


<pre>Redirection 
</pre>
Redirection is a technique that essentially allows commands to either read data from a text file, or save the output to text files. In other words it lets you redirect a command's standard output to a file rather than displaying it on the screen. The best way to understand how piping works, is to see it in action. Lets say we have a file1 which contains the following content:

<pre>
$ cat file1
apple
102
cakes
drinks
bananas
500
301
</pre>

Now we want to sort the lines in this file in alphabetical order, and then store the ordered lines into a file called file2. To start with, lets first check that we can order the content using the "sort" command:

<pre>
$ sort file1
102
301
500
apple
bananas
cakes
drinks
</pre>

Now we need to redirect the standard output to a file instead of outputting it to the screen:

<pre>
$ sort file1 > file2
</pre>

Here we have employed the <mark>></mark> redirection operator to redirect the standard output from "sort file1" to a new file called file2. Lets now look at the contents of file2 to confirm that this has worked:

<pre>
$ cat file2
102
301
500
apple
bananas
cakes
drinks
</pre>

If you alredy have a file called file2 which contains data, then if redirect standard outputs to it using <code>></code> then it will overwrite file:

<pre>
$ echo "oops, I have now overwritten this file" > file2
$ cat file2
oops, I have now overwritten this file
</pre>

If you want to append to an existing file then you can do this using append redirection symbol, <code>>></code>:


<pre>
$ echo "this is a new line" >> file2
</pre>


Now check that this has worked:


<pre>
$ cat file2
oops, I have now overwritten this file
this is a new line
</pre>



If you try to append to a file that doesn't exist, then ">>" will simply create the file and then append the content to it. 

In Linux, the <code>>></code> operator is often used for adding entries to log files. 


<h2>Using redirection to take input from a file
</h2>If you want a command to take in the contents of a file, as it's input, then you use the read-in redirection symbol:

<pre>
$ sort < file1
102
301
500
apple
bananas
cakes
drinks
</pre>

Here, you are instructing "sort" to take in the content of file1, sort it, and then display the output. However the sort command already accepts a filename as a parameter. which means you can achieve the same results like this:

<pre>
$ sort file1
102
301
500
apple
bananas
cakes
drinks
</pre>

For that reason, the "<" isn't used as much since most commands by default already accepts a filename as a parameter. However some people prefer to use "<" for the sake of clarity, hence here are 2 commands that does gives the same result:   

<pre>
$ sort file1 > file2 
</pre>

Here the "sort" command reads file1 as its input, sorts it, and then sends the output to file2.


<pre>
$ sort <file1 >file2 
</pre>

This does the same thing as the previous command, but describes what it is happening more explicitly. 

 
<h2>Combining piping with redirection</h2>
There is a third way to write the above command, and that is by combining piping and redirection, like this:

<pre>
cat file1 | sort > file2 
</pre>

just to check that this has worked:

<pre>
$ cat file2
102
301
500
apple
bananas
cakes
drinks
</pre>

Combining piping and redirection in this way unleashes a whole new way of working on the Linux command line, and is heavily used when writing shell scripts.