---
ID: 115
post_title: 'Python &#8211; Hello world'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/python/python-hello-world
published: true
post_date: 2014-06-14 00:00:00
---
Everytime you run a python command, python does does the following steps in sequence:
<ol>
	<li>Read</li>
	<li>Evaluate</li>
	<li>Print           - if anything needs printing.</li>
	<li>Loopback     - go back to the command line</li>
</ol>
The above is called "repl" for short.


&nbsp;

To print anything, you need to use the "print", which in Python 3, is a function.

[python]
=>=>=> print(&quot;hello world!&quot;)
hello world!
[/python]


Some maths are also really easy to do:

[python]
=>=>=> 5    # this is a comment.
5
=>=>=> 2
2
=>=>=> 2 + 5
7
=>=>=> x = 20
=>=>=> x*5      # note you can leave out white spaces.
100
=>=>=> _  - 1   # &quot;_&quot; is a builtin variable that holds the last thing that has been outputted. 
99
=>=>=>
[/python]

Note: The "_" only works on the command line, but not in scripts. 

With the maths you can see repl in action.