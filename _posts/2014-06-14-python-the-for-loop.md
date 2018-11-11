---
ID: 116
post_title: 'Python &#8211; The For-Loop'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/python/python-the-for-loop
published: true
post_date: 2014-06-14 00:00:00
---
Here is a simple for loop which we'll demonstrate using the <a href="https://docs.python.org/release/1.5.1p1/tut/range.html">range function</a>.

In Pyth+on, a block of code is indicated by white space indents rather than encasing it brackets.

[python]
=>=>=> for i in range(5):
...     x = i * 10      # note the 4 space indent
...     print(x)         
...
0
10
20
30
40
=>=>=>
[/python]

The ":" colon indicates the start of the new block.

There are a couple of conventions regarding white spaces:
<ol>
	<li>indents should be 4 spaces long</li>
	<li>always use space rather than tab to create the whitespace. Also never mix tabs with spaces.</li>
</ol>
These conventions are documeted in Python Enhancement Proposals <a href="http://legacy.python.org/dev/peps/">peps</a>, in particular <a href="http://legacy.python.org/dev/peps/pep-0008/">pep 8</a>.

&nbsp;

Also long lines can be broken over multiple lines by wrapping expressions in parentheses.