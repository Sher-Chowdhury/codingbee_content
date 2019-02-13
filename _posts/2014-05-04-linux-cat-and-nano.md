---
ID: 49
post_title: linux-cat-and-nano
author: admin
post_excerpt: ""
layout: post
permalink: http://localhost:8000/?p=49
published: true
post_date: 2014-05-04 00:00:00
---
 

**Commands:**

cat

nano         # This is a text editor. no need to learn this. vim is much better.

**Config files:**

n/a

**Notes:**

Here are some example using cat (as a text file creator rather than a file content’s outputter):

example 1:

$ cat <<EOF =>testfile     # This lets you start writing stuff, until you type EOF, after that your content gets stored in testfile.

=> this is a testfile

=> another line

=> EOF

example 2:

$ cat <<EOF                    # Here the output is printed directly pack to the command line.

=> this is a testline

=> another line

=> yet another line

=> EOF

this is a testline

another line

yet another line

**Related Services:**

Inserttexthere

 

**Must survive reboot:**

n/a

**Software to install:**

n/a

**GUI tool:**

n/a

**Book ref:**

n/a

**Study guide ref:**

n/a