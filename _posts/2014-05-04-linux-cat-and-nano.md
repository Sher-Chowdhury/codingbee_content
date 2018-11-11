---
ID: 8
post_title: 'Linux &#8211; cat and nano'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/redhat/linux-cat-and-nano
published: true
post_date: 2014-05-04 00:00:00
---
&nbsp;

<strong>Commands:</strong>

cat

nano         # This is a text editor. no need to learn this. vim is much better.

<strong>Config files:</strong>

n/a

<strong>Notes:</strong>

Here are some example using cat (as a text file creator rather than a file content’s outputter):

example 1:

$ cat &lt;&lt;EOF =>testfile     # This lets you start writing stuff, until you type EOF, after that your content gets stored in testfile.

=> this is a testfile

=> another line

=> EOF

example 2:

$ cat &lt;&lt;EOF                      # Here the output is printed directly pack to the command line.

=> this is a testline

=> another line

=> yet another line

=> EOF

this is a testline

another line

yet another line

<strong>Related Services:</strong>

Inserttexthere

&nbsp;

<strong>Must survive reboot:</strong>

n/a

<strong>Software to install:</strong>

n/a

<strong>GUI tool:</strong>

n/a

<strong>Book ref:</strong>

n/a

<strong>Study guide ref:</strong>

n/a