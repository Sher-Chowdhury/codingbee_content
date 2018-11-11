---
ID: 193
post_title: 'Ruby &#8211; The while loop'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-the-while-loop
published: true
post_date: 2014-11-25 00:00:00
---
In most programming languages, while/until/for loops are used quite heavily. These types of loops are also available in Ruby as well, but are surprisingly used only sparingly. That's because two of the most common uses of loops are: 

- to cycle through a collection (e.g. an array) 
- cycle through the same code block of code a set number of times. 

Both of these are better handled using Ruby iterator feature (cover later). 

Here's a simple example of while loop:


<pre>
x = 0
while x <= 10
  puts x
  x+=1
end
</pre>

You can also squeeze this into a single line:

<pre>
x = 0
while x <= 10 do puts x ; x+=1 end
</pre>
Notice, that I used the "do" keyword. You should only use the "do" keyword when writing a while loop into a single line I have also added a ";" to delimit the code block. 

Either of the above two, outputs:

<pre>
PS C:\Temp\irb> ruby .\while.rb
0
1
2
3
4
5
6
7
8
9
10
PS C:\Temp\irb>
</pre>




Now what if you want to do a do-while loop? The difference between a while-loop and a do-while loop is that, that a while-loop won't run the block if the condition is initially met, whereas the do-while loop will run the code block once first before, the condition is checked to see whether it is met or not. This means that with a do-while loop, the code block is run at least once, whereas with a while loop it is possible for the code-block to have never have been run. 


For example, let's say you have:

<pre>
x = 11
while x <= 10
  puts x
  x+=1
end
</pre>

This won't output anything since the while loops prevented the code block to even run. If you want to just run it at least once, then you do this using the do-while code block, as shown here:

<pre>
 x = 11
 
 begin 
   puts x
   x+=1
 end while x <= 10
</pre>

This outputs:

<pre>
PS C:\Temp\irb> ruby .\while.rb
11
PS C:\Temp\irb>
</pre>