---
ID: 196
post_title: 'Ruby &#8211; Iterators and  Blocks'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-iterators-and-blocks
published: true
post_date: 2014-11-26 00:00:00
---
In ruby, you'll encounter an object that is actually a container that contains other objects. An array is the most common example of this. 

In Ruby, you create an array like this:

<pre>
weekdays = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday"]
puts weekdays      # outputs: array
puts weekdays[1]   # outputs: Tuesday
puts weekdays[4]   # outputs: Friday
</pre>

Note: The "class" method is defined is all classes and is used to output the name of the class used to instantiate the given object. 

If you take a look at what methods are in the "array" class that are available to the array, then you will find:

<pre>
irb(main):021:0> weekdays.public_methods.sort
=> [:!, :!=, :!~, :&, :*, :+, :-, :<<, :<=>, :==, :===, :=~, :[], :[]=, :__id__, :__send__, :all?, :any?, :assoc, :at, :chunk, :class, :clear, :clone, :collect, :collect!, :collect_concat, :combination, :compact, :compact!, :concat, :count, :cycle, :define_singleton_method, :delete, :delete_at, :delete_if, :detect, :display, :drop, :drop_while, :dup, <strong>:each</strong>, :each_cons, :each_entry, :each_index, :each_slice, :each_with_index, :each_with_object, :empty?, :entries, :enum_for, :eql?, :equal?, :extend, :fetch, :fill, :find, :find_all, :find_index, :first, :flat_map, :flatten, :flatten!, :freeze, :frozen?, :grep, :group_by, :hash, :include?, :index, :initialize_clone, :initialize_dup, :inject, :insert, :inspect, :ins
tance_eval, :instance_exec, :instance_of?, :instance_variable_defined?, :instance_variable_get, :instance_variable_set,:instance_variables, :is_a?, :join, :keep_if, :kind_of?, :last, :length, :map, :map!, :max, :max_by, :member?, :method, :methods, :min, :min_by, :minmax, :minmax_by, :nil?, :none?, :object_id, :one?, :pack, :partition, :permutation, :pop, :private_methods, :product, :protected_methods, :public_method, :public_methods, :public_send, :push, :rassoc, :reduce, :reject, :reject!, :repeated_combination, :repeated_permutation, :replace, :respond_to?, :respond_to_missing?, :reverse,:reverse!, :reverse_each, :rindex, :rotate, :rotate!, :sample, :select, :select!, :send, :shift, :shuffle, :shuffle!, :singleton_class, :singleton_methods, :size, :slice, :slice!, :slice_before, :sort, :sort!, :sort_by, :sort_by!, :taint, :tainted?, :take, :take_while, :tap, :to_a, :to_ary, :to_enum, :to_s, :transpose, :trust, :uniq, :uniq!, :unshift, :untaint, :untrust, :untrusted?, :values_at, :zip, :|]
irb(main):022:0>

</pre>

 
Here, the one we are interested in is the "each" method.

The "each" method is used a lot in Ruby, and it works a bit like a foreach loop, but much more powerful. 

Here is very simple example of the each-method in action:

<pre>
weekdays = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday"]
weekdays.each do
  puts "This is a weekday"
end
</pre>

Notice that we used the "do...end" block. 

Which outputs:

<pre>
PS C:\Temp\irb> ruby .\block.rb
This is a weekday
This is a weekday
This is a weekday
This is a weekday
This is a weekday
PS C:\Temp\irb>
</pre>

As you can see, the each-method works pretty much like a for-each loop. Now there's some terminology that is used to different parts of this looping code. 

iterator: this is used to refer to the "{variable}.each" statement. 
block : this is used to refer to the do...end block and it's content. You can also replace the "do" and "end" keywords with curly brackets, and it works exactly the same way. However the convention is to only use curly braces for single line blocks, e.g.:

<pre>
weekdays = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday"]
weekdays.each {  puts "This is a weekday" }
</pre>

This again outputs:

<pre>
PS C:\Temp\irb> ruby .\block.rb
This is a weekday
This is a weekday
This is a weekday
This is a weekday
This is a weekday
PS C:\Temp\irb>
</pre>

You can also think of this as follows:

The each method requires an arguement. And this argument is in the form of a block, either do...end or {...}. The each method executes the contents of the block for each item in the iterator. 

However this construct has another vital feature, and that is that the each method can pass parameters into the block. The most obvious thing you'll want to pass in is the array item that is currently active in the iterator. You do this using the pipe notation "|{parameter}|", here's an example:


<pre>
weekdays = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday"]
weekdays.each do |day|
  puts "This is #{day}"
end
</pre>

or alternatively:

<pre>
weekdays = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday"]
weekdays.each { |day|  puts "This is #{day}" }
</pre>

both approach outputs:

<pre>
PS C:\Temp\irb> ruby .\block.rb
This is Monday
This is Tuesday
This is Wednesday
This is Thursday
This is Friday
PS C:\Temp\irb>
</pre>


Notice that the pipe syntax is the very first thing to be specified at the start of the block.