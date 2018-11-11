---
ID: 222
post_title: 'Ruby &#8211; Arrays'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-arrays
published: true
post_date: 2014-12-14 00:00:00
---
<pre>
irb(main):001:0> array1 = ["a","b","c"]
=> ["a", "b", "c"]
irb(main):002:0> array1[0]
=> "a"
irb(main):003:0> array1[1]
=> "b"
irb(main):004:0> array1[2]
=> "c"
irb(main):015:0> array1.class
=> Array
irb(main):016:0> array1.first
=> "a"
irb(main):017:0> array1.last
=> "c"
irb(main):018:0> empty_array=[]
=> []
irb(main):019:0> empty_array.class
=> Array
irb(main):020:0> arr = Array.new(4) # creates an array with 4 place holders.
=> [nil, nil, nil, nil]
irb(main):022:0> arr = Array.new(3, "hello")
=> ["hello", "hello", "hello"]     # creates an array with default values. 
# Note the above three elements are all referencing the same object. This means 
# that if you apply a method to one of thes elements, it will impact all the elements:

irb(main):020:0> arr[0].upcase!
=> "HELLO"
irb(main):021:0> arr
=> ["HELLO", "HELLO", "HELLO"]

# However you can make one element point to a different object:

irb(main):022:0> arr[0] = "goodbye"
=> "goodbye"
irb(main):023:0> arr
=> ["goodbye", "HELLO", "HELLO"]
 
# If you don't want all array elements referencing the same object, then create the array using the block-form style, like this:

irb(main):026:0> arr1 = Array.new (3)  {"abc"}  
=> ["abc", "abc", "abc"]    
# Here the {...} get's iterated every time the Array generates an element for the array. 
# The block's return-value is stored as an object and the newly generated element points to this.  
# That therefore means:
irb(main):027:0> arr1[0].upcase!
=> "ABC"
irb(main):028:0> arr1
=> ["ABC", "abc", "abc"]

# This approach also lets you created multi-dimensinaol arrays, e.g.:


irb(main):044:0* arr1 = Array.new (3)  {Array.new (3)}
=> [[nil, nil, nil], [nil, nil, nil], [nil, nil, nil]]
irb(main):045:0> arr1[1]
=> [nil, nil, nil]
irb(main):046:0> arr1[1][2]
=> nil

# You can also convert a string into an array, using the "%w" notation:

irb(main):068:0> arr1 = %w(hello amazing world of Ruby)   # note, we don't use quotes here. 
=> ["hello", "amazing", "world", "of", "Ruby"]
irb(main):069:0> arr1[0]
=> "hello"                  # by default, the white space is used as the delimiter. 

# you can use a black space to prevent "%w" to use a certain space as a delimter. 

irb(main):077:0* arr1 = %w(hello\ amazing\ world of Ruby)
=> ["hello amazing world", "of", "Ruby"]
irb(main):078:0> arr1[0]
=> "hello amazing world"

# Now if you do:

irb(main):085:0* arr1 = %w(2+2 is #{2+2})
=> ["2+2", "is", "\#{2+2}"]

# This happens because by default, %w placed the string into single quotes before 
#processing into an array. If you want to use double-quotes instead, in order to evaluate 
# the string before splitting it into an array, then use "%W":

 
irb(main):086:0> arr1 = %W(2+2 is #{2+2})
=> ["2+2", "is", "4"]
irb(main):087:0>

# You can also use the %i notation to generate an array containing symbols (data type):

irb(main):098:0> arr1 = %i(up down left right)     # note this only work in ruby 2.0+

# You can also access arrays in reverse-order. 
irb(main):117:0> arr = ["a","b","c","d"]
=> ["a", "b", "c", "d"]
irb(main):118:0> arr[-1] 

# You can also obtain a subset of an array:

irb(main):135:0* arr = ["a","b","c","d"]
=> ["a", "b", "c", "d"]
irb(main):136:0> arr[0..2]      # here we use the array's positional numbers. 
=> ["a", "b", "c"]

# You can also replace parts of an array:

irb(main):186:0* arr = ["a","b","c","d","e"]
=> ["a", "b", "c", "d", "e"]
irb(main):187:0> arr[1]
=> "b"
irb(main):188:0> arr[2]
=> "c"
irb(main):189:0> arr[3]
=> "d"
irb(main):190:0> arr[1..3] = ["hello", "amazing","world"]
=> ["hello", "amazing", "world"]
irb(main):191:0> arr
=> ["a", "hello", "amazing", "world", "e"]

# You can also append an array, like this:

irb(main):192:0> arr << "f"
=> ["a", "hello", "amazing", "world", "e", "f"]

# You can also add 2 arrays together using the plus operator:

irb(main):194:0> arr = ["the","quick"] + ["brown","fox"]
=> ["the", "quick", "brown", "fox"]
irb(main):195:0> arr[3]
=> "fox"
irb(main):196:0>

# you can also duplicate an array:

irb(main):196:0> arr = ["the","quick"] * 3
=> ["the", "quick", "the", "quick", "the", "quick"]

# The "*" can also be used to convert a an array into a string:

irb(main):202:0> ["the","quick", "brown","fox"] * "_"
=> "the_quick_brown_fox"
irb(main):203:0> ["the","quick", "brown","fox"] * " "
=> "the quick brown fox"

# You can also remove (i.e.) particular elements  of an array like this:

irb(main):204:0> [1,35,2,89,3,520,4] - [35,89,520]
=> [1, 2, 3, 4]



</pre>

Now let's take a look at some handy array methods:


<pre>

irb(main):104:0* arr = ["a","b","c","d"]
=> ["a", "b", "c", "d"]
irb(main):112:0> arr.first    # accesses the array's first element. Alternatively use arr[0]
=> "a"
irb(main):113:0> arr.last     # accesses the array's last element. Alternatively use arr[-1]
=> "d"
</pre>


Earlier we saw the use of the {...} block form style in conjunction with an array. This can be used to pass in the array's current iteration's positional number like this:

<pre>
irb(main):205:0> arr1 = Array.new (3)  {|number| number = number*2}
=> [0, 2, 4] 
</pre>

This is really powerfull becuase you can put a multiline code-block within the {...} block. What we are seeing here, is a taster of the use of a new interface, which is known as "enumerables". We'll cover more about enumerables in the next lesson.  



Here's an exmaple of how to generate an array from a method:

<pre>
class Testclass

  def arraygen (value)
    #puts value
	arr1 = []
    for i in [1,2,3]  
	  result = i + value
      arr1 << result
	  
    end
    arr1
  end
end

sampleclass = Testclass.new

arr = sampleclass.arraygen(7)

puts arr[1]
</pre>




See also:
Also checkout the ruby <a href="http://www.ruby-doc.org/core-2.1.5/Array.html">array official documentation</a>.

http://www.tutorialspoint.com/ruby/ruby_arrays.htm