---
ID: 199
post_title: 'Ruby &#8211; Controlling loops using next/break/redo'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-controlling-loops-using-nextbreakredo
published: true
post_date: 2014-11-26 00:00:00
---
So far we have looked at various types of loops, e.g. while loops, for loops,....etc. 

What ever type of loop you are using, there will be times when you want to one of the following:

<ul>
	<li>skip to the next iteration while part way through the current interation - this is done using the "next" keyword</li>
	<li>exit the loop early - this is done using the "break" keyword.</li>
	<li>redo the current iteration - this is done using the "redo" keyword.</li>
</ul>

We will demonstrate the next/break/redo keywords using a simple for-loop (although they can work in any type of loop)

<h2>Skipping a current loop iteration - using "next" </h2>
<pre>
for i in (1..5)
  next if i == 3
  puts i
end
</pre>

This outputs:

<pre>
PS C:\Temp\irb> ruby .\nextloop.rb
1
2
4
5
PS C:\Temp\irb>
</pre>




<h2>Exiting the whole loop early - using "break" </h2>

Here's an example:

<pre>
for i in (1..5)
  break if i == 3
  puts i
end
</pre>

This outputs:

<pre>
PS C:\Temp\irb> ruby .\breakloop.rb
1
2
PS C:\Temp\irb>
</pre>

The break keyword has an extra feature for catching an output from a loop when the break keyword gets triggered, here's an example of how this works:

<pre>
answer = for i in (1..5)
  break "loop exited early" if i == 3
  puts i
end         # outputs: 1 2

puts answer    # this outputs "loop exited early"

</pre>

This technique could be useful for various purposes, including capturing possible error messages and storing it into logs. 


<h2>Redoing the current iteration of a loop - using "redo" </h2>

Here's an example of the redo key-word in action:

<pre>
for i in (1..5)
  i += 1            # I had to add this line in to avoid an infinite loop. 
  redo if i == 3
  puts i
end
</pre>

This outputs:
<pre>
PS C:\Temp\irb> ruby .\redoloop.rb
2
4
4
5
6
PS C:\Temp\irb>
</pre>