---
ID: 191
post_title: 'Ruby &#8211; The &#8220;and&#8221; and &#8220;or&#8221; operators'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-the-and-and-or-operators
published: true
post_date: 2014-11-25 00:00:00
---
The "and" operatory can be used to string together several methods, here's an example:
<pre>def hello
  puts "Hello"
  "returing: not-nil or false"
end

def aloha
  puts "aloha"
  # "returing: not-nil or false"
end

def bonjour
  puts "bonjour"
  "returing: not-nil or false"
end


hello and aloha and bonjour
</pre>
This outputs:
<pre>PS C:\Temp\irb&gt; ruby .\and.rb
Hello
aloha
bonjour
PS C:\Temp\irb&gt;
</pre>


If one of the method returns false or nil, then any methods following it will no longer run, here's an example:
<pre>def hello
  puts "Hello"
  "returning: not-nil or false"
end

def aloha
  puts "aloha"
  # "returning: not-nil or false"      here we are now doing a nil output. 
end

def bonjour
  puts "bonjour"
  "returning: not-nil or false"
end


hello and aloha and bonjour
</pre>
Now the output is:
<pre>PS C:\Temp\irb&gt; ruby .\and.rb
Hello
aloha
PS C:\Temp\irb&gt;

</pre>

Notice this time that the bonjour method did not run this time.

Now here's the "or" assignment in action:

<pre>
def hello
  puts "Hello"
  "returning: not-nil or false"
end

def aloha
  return false      # Here we are terminating the method early by explictly using the "return" keyword. 
  puts "aloha"
end

def bonjour
  puts "bonjour"
  "returning: not-nil or false"
end

hello and aloha or bonjour
</pre>


Here's a few things to note:
<ul>
	<li>&amp;&amp; and || have much higher priority over "and" and "or"</li>
	<li>&amp;&amp; has higher priority over ||</li>
	<li>"and" and "or" have equal priority over each other.</li>
</ul>