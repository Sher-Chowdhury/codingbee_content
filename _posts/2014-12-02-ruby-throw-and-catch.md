---
ID: 204
post_title: 'Ruby &#8211; Throw and Catch'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-throw-and-catch
published: true
post_date: 2014-12-02 00:00:00
---
In Ruby, there is an alternative to exceptions handling, and that is using the "throw/catch" system.

Throw/catch are specifically designed for getting out of nested loops. 

For example, say of you are 4 loops deep, and you only want exit out to the second loop, then you can use throw/catch to do this. 

 

Here's an example:

<pre>

catch :abort do 
  [1,2,3,4,5,6,7].each do |number|
    while number == 5
	  puts number
	  puts "need to abort infinite loop"
	  throw :abort 
    end
	  puts number
  end
end

</pre>

The symbol ":abort" is used here in the sense of a label. It is used here to pair up the throw statement with the corresponding abort statement. 


throw-catch even works across methods. 

Throw/catch is just an alternative option you can use. But it's unlikely that you will use it that heavily.