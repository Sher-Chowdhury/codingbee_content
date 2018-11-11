---
ID: 224
post_title: 'Ruby &#8211; Hashes'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-hashes
published: true
post_date: 2014-12-14 00:00:00
---
Hashes are instantiated from the Hash class. 

Haskes are essentially an array where each element's value is given it's own name, rather than an array's default 1,2,3 positional numbers (i.e. number index). 

Hashes are essentially a list of key/value pairs. 

<pre>
my_hash = {}    # Using {} brackets creates an empty hash. 

# Now here's an example:

contacts = {
  "ceo" => "Mr Smith",        # notice we used double-quotes for the keys. 
  "cio" => "Mrs Jones",
  "cfo" => "Mr Anderson"      # note 
}

puts contacts["ceo"]        # Outputs: "Mr Smith"     # also notice we used double quotes here too. 


# You can rewrite this using this alternative syntax:

contacts = {
  ceo:  "Mr Smith",            # In this syntax, the keys are automatically assumed to be 
  cio: "Mrs Jones",            # symbols  
  cfo: "Mr Anderson"
}

puts contacts[:ceo]         # notice that we now call via a symbol as the key.

# It makes more sense to use "symbols" in conjunction with hashes. 

# you can also append a new key/value pair to an existing hash, like this:


contacts = {
  ceo:  "Mr Smith",
  cio: "Mrs Jones",
  cfo: "Mr Anderson"
}

puts contacts         # outputs:  "{:ceo=>"Mr Smith", :cio=>"Mrs Jones", :cfo=>"Mr Anderson"}"

contacts[:chairman] = "Mr Donald"

puts contacts[:chairman]   # outputs:   "Mr Donald"

puts contacts         # "{:ceo=>"Mr Smith", :cio=>"Mrs Jones", :cfo=>"Mr Anderson", :chairman=>"Mr Donald"}"

# Now if you try to pull out a value using a non existant key, then by default you will get "nil":


PS C:\Temp\irb> irb
irb(main):001:0> contacts = {
irb(main):002:1*   ceo:  "Mr Smith",
irb(main):003:1*   cio: "Mrs Jones",
irb(main):004:1*   cfo: "Mr Anderson"
irb(main):005:1> }
=> {:ceo=>"Mr Smith", :cio=>"Mrs Jones", :cfo=>"Mr Anderson"}
irb(main):006:0> contacts[:ceo]
=> "Mr Smith"
irb(main):007:0> contacts[:manager]
=> nil
irb(main):008:0>


# You can change the default response by using the new method to instantiate the hash
# and feed in a default value:

irb(main):014:0* contacts = Hash.new("Unassigned")
=> {}
irb(main):015:0> contacts[:ceo] = "Mr Smith"
=> "Mr Smith"
irb(main):016:0> contacts[:cio] = "Mrs Jones"
=> "Mrs Jones"
irb(main):017:0> contacts[:cfo] = "Mr Anderson"
=> "Mr Anderson"
irb(main):018:0> contacts
=> {:ceo=>"Mr Smith", :cio=>"Mrs Jones", :cfo=>"Mr Anderson"}
irb(main):019:0> contacts[:cio]
=> "Mrs Jones"
irb(main):020:0> contacts[:manager]
=> "Unassigned"
irb(main):021:0>
</pre>




# You can also pass each key/value pair into a {..} block in turn using the each method:

<pre>
contacts = {
  ceo:  "Mr Smith",
  cio: "Mrs Jones",
  cfo: "Mr Anderson"
}

contacts.each {|mykey, myvalue| puts "The key is #{mykey} and the value is #{myvalue}" }

# this will output:
The key is ceo and the value is Mr Smith
The key is cio and the value is Mrs Jones
The key is cfo and the value is Mr Anderson
=> {:ceo=>"Mr Smith", :cio=>"Mrs Jones", :cfo=>"Mr Anderson"}
irb(main):045:0> 

</pre>

Notice that we defined 2 block arguments, "mykey" and "myvalue", that's because we know that each in each iteratio, 2 bits of info will be passed into the block, hence we are able to capture them intelligently using this approach. 

Using multiple block arguments are commonly used.