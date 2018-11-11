---
ID: 225
post_title: 'Ruby &#8211; Ranges'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-ranges
published: true
post_date: 2014-12-14 00:00:00
---
This Range class basically generates a sequence of numbers:

<pre>
irb(main):045:0> (1..10)
=> 1..10
irb(main):046:0> (1..10).begin
=> 1
irb(main):047:0> (1..10).end
=> 10
irb(main):048:0> (1...10).begin
=> 1
irb(main):049:0> (1..10)
=> 1..10
irb(main):050:0> (1..10).class
=> Range
irb(main):051:0> (1..10).begin
=> 1
irb(main):052:0> (1..10).end
=> 10
irb(main):054:0> (1..10).include?(7)
=> true
irb(main):055:0> (1..10).include?(12)
=> false

# If you want to exclude the last value in a range, then use the ... notation while using the block-iterater:

irb(main):099:0> (1..5).each {|v| puts v}
1
2
3
4
5
=> 1..5
irb(main):100:0> (1...5).each {|v| puts v}
1
2
3
4
=> 1...5
irb(main):101:0>

 
</pre>


A range also includes the enumerable module, this means you can do thing like as follows:

<pre>
# Use a range to create an array:
irb(main):056:0> (1..10).map {"hello"}
=> ["hello", "hello", "hello", "hello", "hello", "hello", "hello", "hello", "hello", "hello"]

# or:

irb(main):058:0> (1..10).map {|number| number * 2}
=> [2, 4, 6, 8, 10, 12, 14, 16, 18, 20]

# You can also iterate through strings:

irb(main):080:0* ("aa".."ad").each {|v| puts v}
aa
ab
ac
ad
=> "aa".."ad"

</pre>

ranges also has a good integration with case-statements:

<pre>
# If we have the following script:
def bignumberchecker (value)
  case value
  when 1..10 
    puts "small number: #{value}"
  when 11..100
    puts "big number: #{value}"
  else
    puts "unknown number size: #{value}"
  end  
end

bignumberchecker 7
bignumberchecker 70
bignumberchecker 700

# The the output is:
PS C:\Temp\irb> ruby hashes.rb
small number: 7
big number: 70
unknown number size: 700

</pre>