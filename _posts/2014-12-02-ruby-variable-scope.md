---
ID: 205
post_title: 'Ruby &#8211; Variable Scope'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-variable-scope
published: true
post_date: 2014-12-02 00:00:00
---
previously we saw that any variables set in a method will be confined to that method's scope. Also a method can't access any variables outside it's scope. e.g.:


<pre>
website_name = "codingbee"
puts website_name

def normal_welcome
  # puts website_name           # this will output an error message, hence commented out.  
  website_name = "google"
  puts website_name
end

normal_welcome

puts website_name
</pre>

This outputs:

<pre>
PS C:Tempirb> ruby .scope.rb
codingbee
google
codingbee
PS C:Tempirb>
</pre>

However this rule doesn't apply to lower level blocks such as:

<ul>
	<li>if-else blocks</li>
	<li>loop blocks</li>
	<li>begin-end blocks</li>
</ul>

Here's some examples:

<pre>
number = 5

if number < 10
  a = 20
  puts a
end

puts a

begin 
   b = 25
   puts b
end

puts b

c = 1
while c < 5
  c +=1
  puts c
end

puts c
</pre>

This outputs:

<pre>
PS C:\Temp\irb> ruby .\scope.rb
20
20
25
25
2
3
4
5
5
PS C:\Temp\irb>
</pre>

However blocks are an exception to this and they do introduce scope:

<pre>
1.upto(10) { |i|
  a = i
  puts i
  puts a
}
  
# puts i    # error: undefined variable. 
# puts a    # error: undefined variable.    
</pre>

this outputs:

<pre>
PS C:\Temp\irb> ruby .\blockscope.rb
1
1
2
2
3
3
4
4
5
5
6
6
7
7
8
8
9
9
10
10
PS C:\Temp\irb>
</pre>