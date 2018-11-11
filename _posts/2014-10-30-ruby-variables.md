---
ID: 164
post_title: 'Ruby &#8211; Variables'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-variables
published: true
post_date: 2014-10-30 00:00:00
---
Convention: You create variables in all lower case and with underscores, e.g.


<pre>
# This is wrong:
TestVariable = 7

# This is correct:
test_variable = 7
</pre>


To create a null (aka nil) variable, you do:
<pre>
irb(main):333:0* test_variable = nil
=> nil
irb(main):334:0> test_variable.class
=> NilClass

# The "nil?" is actually a name of an instance variable that simply returns a true/false boolean. 
irb(main):335:0> test_variable.nil?
=> true
irb(main):336:0>
</pre>

The question mark doesn't have a special meaning here, it is just part of the method's name:

<pre>
irb(main):337:0> test_variable.public_methods.sort
=> [:!, :!=, :!~, :&, :<=>, :==, :===, :=~, :^, :__id__, :__send__, :class, :clone, :define_singleton_method, :display, :doublingnumber, :dup, :enum_for, :eql?, :equal?, :extend, :freeze, :frozen?, :hash, :initialize_clone, :initialize_dup, :inspect, :instance_eval, :instance_exec, :instance_of?,:instance_variable_defined?, :instance_variable_get, :instance_variable_set, :instance_variables, :is_a?, :kind_of?, :method, :methods, <strong>:nil?</strong>, :object_id, :private_methods, :protected_methods, :public_method, :public_methods, :public_send, :rationalize, :respond_to?, :respond_to_missing?, :send, :singleton_class, :singleton_methods, :taint, :tainted?, :tap, :to_a, :to_c, :to_enum, :to_f, :to_i, :to_r, :to_s, :trust, :untaint, :untrust, :untrusted?, :|]
irb(main):338:0>
</pre>

Method naming convention: methods ending with a "?" usually means it will do a true/false test. methods ending with a "!" usually means that it will make some kind of permenant change to the object. 

E.g., the string class has two methods called "upcase" and "upcase!", let's try them out:

<pre>
irb(main):348:0> a_string.public_methods.sort
=> [:!, :!=, :!~, :%, :*, :+, :<, :<<, :<=, :<=>, :==, :===, :=~, :>, :>=, :[], :[]=, :__id__, :__send__, :ascii_only?, :between?, :bytes, :bytesize, :byteslice, :capitalize, :capitalize!, :casecmp, :center, :chars, :chomp, :chomp!, :chop, :chop!, :chr, :class, :clear,:clone, :codepoints, :concat, :count, :crypt, :define_singleton_method, :delete, :delete!, :display, :doublingnumber, :downcase, :downcase!, :dump, :dup, :each_byte, :each_char, :each_codepoint, :each_line, :empty?, :encode, :encode!, :encoding, :end_with?, :enum_for, :eql?, :equal?, :extend, :force_encoding, :freeze, :frozen?, :getbyte, :gsub, :gsub!, :hash
, :hex, :include?, :index, :initialize_clone, :initialize_dup, :insert, :inspect, :instance_eval, :instance_exec, :instance_of?, :instance_variable_defined?, :instance_variable_get, :instance_variable_set, :instance_variables, :intern, :is_a?, :kind_of?, :length, :lines, :ljust, :lstrip, :lstrip!, :match, :method, :methods, :next, :next!, :nil?, :object_id,:oct, :ord, :partition, :prepend, :private_methods, :protected_methods, :public_method, :public_methods, :public_send, :replace, :respond_to?, :respond_to_missing?, :reverse, :reverse!, :rindex, :rjust, :rpartition, :rstrip, :rstrip!, :scan, :send, :setbyte, :singleton_class, :singleton_methods, :size, :slice, :slice!, :split, :squeeze, :squeeze!, :start_wit
h?, :strip, :strip!, :sub, :sub!, :succ, :succ!, :sum, :swapcase, :swapcase!, :taint, :tainted?, :tap, :to_c, :to_enum, :to_f, :to_i, :to_r, :to_s, :to_str, :to_sym, :tr, :tr!, :tr_s, :tr_s!, :trust, :unpack, :untaint, :untrust, :untrusted?, <strong>:upcase, :upcase!</strong>, :upto, :valid_encoding?]
irb(main):349:0> a_string.upcase
=> "HELLO WORLD"            # Displays modified output with changing the original
irb(main):350:0> a_string
=> "hello world"
irb(main):351:0> a_string.upcase!   # This time the change is permanent
=> "HELLO WORLD"
irb(main):352:0> a_string
=> "HELLO WORLD"
irb(main):353:0>
</pre>


In Ruby, variables are essentially labels to an object:


<pre>
irb(main):001:0> a = "abc"
=> "abc"
irb(main):002:0> b =a
=> "abc"
irb(main):003:0> b
=> "abc"
irb(main):004:0> a.upcase
=> "ABC"
irb(main):005:0> a
=> "abc"
irb(main):006:0> a.upcase!
=> "ABC"
irb(main):007:0> a
=> "ABC"
irb(main):008:0> b
=> "ABC"
</pre>

The reason that ruby takes this approach is so that it makes more efficient use of memory. To prove that both a and b are 2 doors to enter the same room, do:

<pre>
irb(main):009:0> a.object_id
=> 19946700
irb(main):010:0> b.object_id
=> 19946700
irb(main):011:0>
</pre>


Note, the same isn't true for integers, because integers doesn't use up as much memory as a string can. This is similar to <a href="http://codingbee.net/tutorials/c/c-variables-types/" title="c# -  What are Variables Types">c# value types and reference types</a>.

If you want b to be seperate from a, the you can do:

<pre>
b = a.clone
</pre>



There are actually different types of variables:
<ul>
	<li>local variables</li>

	<li>global variables</li>
	<li>instance variables</li>
</ul>

The examples shown above are only examples of local variables. 



http://www.tutorialspoint.com/ruby/ruby_variables.htm

http://en.wikibooks.org/wiki/Ruby_Programming/Syntax/Variables_and_Constants