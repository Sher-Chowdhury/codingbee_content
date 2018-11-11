---
ID: 209
post_title: 'Ruby &#8211; Escaping character in strings'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-escaping-character-in-strings
published: true
post_date: 2014-12-02 00:00:00
---
You can escape characters that are in quotes. In most languages you can only escape things that are in double quotes. However in Ruby you can escape in single-quotes. 

Here's an example:

<pre>

irb(main):023:0> string = 'a dog\'s bone'
=> "a dog's bone"
irb(main):024:0> puts string
a dog's bone
=> nil
irb(main):025:0>
irb(main):025:0> string = "a dog\'s bone"
=> "a dog's bone"
irb(main):026:0> puts string
a dog's bone
=> nil
</pre>


Instead of using single/double quotes, you can specify your own "quote-encaser". This is done using the "%q" syntax, where the "q" stands for quote. But this encaser has to be a non-alphanumeric character, e.g. !,?,-....etc. Also encaser's that comes in pair's such as [] or {}, can also be used in the form of a pair. e.g.:

<pre>
irb(main):050:0* string = %q-a dog's bone-
=> "a dog's bone"
irb(main):051:0> puts string
a dog's bone
=> nil
irb(main):052:0> string = %q[a dog's bone]
=> "a dog's bone"
irb(main):053:0> puts string
a dog's bone
=> nil
irb(main):054:0> string = %q(a dog's bone)
=> "a dog's bone"
irb(main):055:0> puts string
a dog's bone
=> nil
irb(main):056:0> string = %q+a dog's bone+
=> "a dog's bone"
irb(main):057:0> puts string
a dog's bone
=> nil
</pre>
 
The advantage of using the %q is that it means that you don't need to do as much escaping. 



<h2>Double quotes</h2>
Double quotes works like single quotes, but it lets you ordinary characters special meaning by using back-slash to escape them. It also gives does the reverse for special characters. However one of the most important thing you can do is embed values of variables into the double-quotes, using the #{variable-name} notation.  

e.g.:

<pre>

irb(main):077:0* string = "a dog's bone"
=> "a dog's bone"
irb(main):078:0> puts string
a dog's bone
=> nil
irb(main):079:0> string = "a dog\"s bone"
=> "a dog\"s bone"
irb(main):080:0> puts string
a dog"s bone
=> nil
irb(main):081:0> string = "a dog's \n bone"
=> "a dog's \n bone"
irb(main):082:0> puts string
a dog's
 bone
=> nil
irb(main):083:0> name = "Jack"
=> "Jack"
irb(main):084:0> string = "The dog is called: \t #{name}"   # here we embeded a variable and used tab. 
=> "The dog is called: \t Jack"
irb(main):085:0> puts string
The dog is called:       Jack
=> nil
irb(main):086:0>
</pre> 

You can also do calculations inside the #{...} notation, e.g.:

<pre>
irb(main):086:0> num1 = 8
=> 8
irb(main):087:0> num2 = 5
=> 5
irb(main):088:0> puts "the sum is: #{num1 + num2}"
the sum is: 13
=> nil
irb(main):089:0>

</pre>