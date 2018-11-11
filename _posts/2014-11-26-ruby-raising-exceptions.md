---
ID: 201
post_title: 'Ruby &#8211; Raising Exceptions'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-raising-exceptions
published: true
post_date: 2014-11-26 00:00:00
---
Note: this post needs to be investigate further. 

So far we have looked at how to capture an error and handle/resolve it if possible. 

However you can write code that raisez an error within your own code. This opens up the possibility of then writing a corresponding error handling for it later down the road once you have figured out how to handle it. Or alternatively refactor the code so that error doesn't need to be raised in the first place (if possible).

 

<pre>
class CustomException < StandardError

  # custom_message = "heeeeeeeeeeeeeeeelp!!!!!!!!!"
  def custom_message
    puts "something went very very wrong."
  end

end



class Duck
  def generic_method
    puts "method has started"
    begin
      puts "hello"
      randomNonsense
      puts "world"
    rescue 
      puts "Ruby failed to understand what randomNonsense is. Some code is placed here to resolve this"
	  raise CustomException, "something went wrong"
    end
    puts "method has completed"
  end
end 

donald = Duck.new

donald.generic_method


</pre>

This outputs:


<pre>
PS C:\Temp\irb> ruby .\raise-exceptions.rb
method has started
hello
Ruby failed to understand what randomNonsense is. Some code is placed here to resolve this
./raise-exceptions.rb:21:in `rescue in generic_method': something went wrong (CustomException)
        from ./raise-exceptions.rb:15:in `generic_method'
        from ./raise-exceptions.rb:29:in `<main>'

</pre>


Note, you actually attach an if-statement at the end of the raise command like this:

<pre>
raise CustomException, "something went wrong" if a_method_that_returns_true_or_false?
</pre>