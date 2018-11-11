---
ID: 200
post_title: 'Ruby &#8211; Exceptions Handling'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-exceptions-handling
published: true
post_date: 2014-11-26 00:00:00
---
"Exceptions" is just another word for "Error"

In programming, error can occur, some of these errors can be handled, which means that the program can carry on working without by fixing these errors by itself. 

For example, lets say you have a online web form that needs to be filled in by the user. This form has several mandatory fields/check-boxes/dropdown-lists.

In this situation a user might fail to complete the some of teh mandatory fields and try to submit. To overcome this you can add some error handling in the form of some javasctript code, so that if user does this then a pop window prompts the user to complete the remaining mandatory fields. 


However the worst case scenario is that, an error can't be handled, in which we need to "raise the exception", which is covered later. 




Now let's see how we can apply error handling in ruby, you can use if-statement for simple error handling. Let say you want to install a gem, only if gem is not already installed, here's a simply way to do this:

<pre>
if puppetversion = ENV['PUPPET_GEM_VERSION'] 
  puts "puppet gem is already installed" 
else
  gem 'puppet'
end
</pre>

Here, the if-condition attempts to create a non-nil variable using an environment variable. This has a 0-or-1 exit status. The if-else clause runs depending on what the exit code is. This is a very simple error-handling scenario, the above type of logic is typically added to a gem file.







Now let's say we have:


<pre>
def generic_method
  puts "method has started"
  begin
    puts "hello"
    randomNonsense
    puts "world"
  end
  puts "method has completed"
end

generic_method

</pre>

This will obviously fail, and it gives the following output:

<pre>
PS C:\Temp\irb> ruby .\exceptionhandling.rb
method has started
hello
./exceptionhandling.rb:5:in `generic_method': undefined local variable or method `randomNonsense' for main:Object (NameError)
        from ./exceptionhandling.rb:11:in `<main>'
PS C:\Temp\irb>
</pre>

Now to add some error handling, we need to insert the "rescue" clause, like this:

<pre>
def generic_method
  puts "method has started"
  begin
    puts "hello"
    randomNonsense
    puts "world"
  rescue
    puts "Ruby failed to understand what randomNonsense is. Some code is placed here to resolve this"
  end
  puts "method has completed"
end

generic_method
</pre>

This now outputs:

<pre>
PS C:\Temp\irb> ruby .\exceptionhandling.rb
method has started
hello
Ruby failed to understand what randomNonsense is. Some code is placed here to resolve this
method has completed
PS C:\Temp\irb>
</pre>

This time the failure was encountered, and it got handled, so that the method was allowed to continue running. 

So far we only applied the rescue clause, to only part of the method, which is encased in the begin-end block. You can also actually apply the rescue clause to the method as a whole:

<pre>
def generic_method
  puts "method has started"
  puts "hello"
  randomNonsense
  puts "world"
  puts "method has completed"
  true     # this could be useful for the caller. hence entered here as good practice. 
rescue
  puts "Ruby failed but not sure why. Some code is placed here to resolve this"
  false    # this could be useful for the caller. hence entered here as good practice. 
end

generic_method
</pre>

This outputs:

<pre>
PS C:\Temp\irb> ruby .\exceptionhandling.rb
method has started
hello
Ruby failed but not sure why. Some code is placed here to resolve this
PS C:\Temp\irb>
</pre>

Apply a rescue clause to a whole method has a drawback, which is that it makes it harder to determine which part of the method failed. 

However the good news is that you can use the "rescue" clause to capture the error information that occured during runtime, aka RuntimeError. This error information  actually exists in the form of an object. There are a number of different types of error class's that an error object can be instantiated from, the class that is used depends on the type of error encountered that occured during runtime. All the error classes are themselve objects too, and they are instantiated from a class called "StandardError". StandardError in turn belongs to a more general class called "Exception".


Now Here's how tio capture the error-information using the rescue clause:

<pre>
def generic_method
  puts "method has started"
  puts "hello"
  randomNonsense
  puts "world"
  puts "method has completed"
rescue StandardError => some_error
  puts "Ruby failed but not sure why. Some code is placed here to resolve this"
  puts some_error.class               # outputs: NameError, which is one of a number of runtime based error-classes. 
  puts some_error.class.superclass    # outputs: StandardError
  puts some_error.class.superclass.superclass   # outputs: Exception
  puts some_error.class.superclass.superclass.superclass   # outputs: Object
end

generic_method
</pre>

The "StandardError => some_error" basically means that if an error is generated from the  "StandardError" class, then capture it into a variable called "some_error"

Now let's see what public_methods are available for the NameError class:

 
<pre>

def generic_method
  puts "method has started"
  puts "hello"
  randomNonsense
  puts "world"
  puts "method has completed"
rescue StandardError => some_error           # here we are capturing the error into a variable. 
  puts "Ruby failed but not sure why. Some code is placed here to resolve this"
  puts some_error.public_methods.sort
  
end

generic_method

</pre>

This outputs:

<pre>
PS C:\Temp\irb> ruby .\exceptionhandling.rb
method has started
hello
Ruby failed but not sure why. Some code is placed here to resolve this
!
!=
!~
<=>
==
===
=~
__id__
__send__
<strong>backtrace</strong>
class
clone
define_singleton_method
display
dup
enum_for
eql?
equal?
exception
extend
freeze
frozen?
hash
initialize_clone
initialize_dup
inspect
instance_eval
instance_exec
instance_of?
instance_variable_defined?
instance_variable_get
instance_variable_set
instance_variables
is_a?
kind_of?
<strong>message</strong>
method
methods
name
nil?
object_id
private_methods
protected_methods
public_method
public_methods
public_send
respond_to?
respond_to_missing?
send
set_backtrace
singleton_class
singleton_methods
taint
tainted?
tap
to_enum
to_s
trust
untaint
untrust
untrusted?
PS C:\Temp\irb>
</pre>

Above I have highlighted 2 of the more useful methods, message (which outputs the content of the actual error message) and "backtrace", which outputs the line number where the failure occurred. 

Hence we can now do this:


<pre>
def generic_method
  puts "method has started"
  puts "hello"
  randomNonsense
  puts "world"
  puts "method has completed"
rescue StandardError => some_error           # here we are capturing the error into a variable. 
  puts "Ruby failed but not sure why. Some code is placed here to resolve this"
  puts "The 'message' method outputs:"
  puts some_error.message
  puts "The 'backtrace' method outputs:"
  puts some_error.backtrace
end

generic_method
</pre>

This outputs:

<pre>
PS C:\Temp\irb> ruby .\exceptionhandling.rb
method has started
hello
Ruby failed but not sure why. Some code is placed here to resolve this
The 'message' method outputs:
undefined local variable or method `randomNonsense' for main:Object
The 'backtrace' method outputs:
./exceptionhandling.rb:4:in `generic_method'
./exceptionhandling.rb:15:in `<main>'
PS C:\Temp\irb>
</pre>

So far we have looked at capturing errors generated by the StandardError class. However you can also create your own custom error classes, e.g. you can create a class called CodingBeeError, which is a child of StandardError.  

With this approach, can set up multiple rescue clauses, each handling a different kind of error message. 

Here is an example of how this would look like:


<pre>
def generic_method
  puts "method has started"
  puts "hello"
  randomNonsense
  puts "world"
  puts "method has completed"
rescue CodingBeeError
  puts "My personal custom error message"
rescue StandardError
  puts "Ruby failed but not sure why. Some code is placed here to resolve this"
end

generic_method
</pre>

I can't run the above yet, becuase we haven't defined the CodingBeeError class yet. 

Note, you should insert the child classes before their respective parent classes, otherwise the lower child rescue clauses gets ignored.  


Like the while and if statements, you also have modifiers for the rescue clause, here's an example:


<pre>
def rescuing_method
  puts "Ruby failed to understand what randomNonsense is. Some code is placed here to resolve this"
end 


def generic_method
  puts "method has started"
  begin
    puts "hello"
    randomNonsense
    puts "world"                # this won't run now, as method exits as soon as error encountered. 
  end
  puts "method has completed"    # this won't run now for same reason. 
end

generic_method rescue rescuing_method
</pre>


This outputs:

<pre>
PS C:\Temp\irb> ruby .\rescue-modifier.rb
method has started
hello
Ruby failed to understand what randomNonsense is. Some code is placed here to resolve this
PS C:\Temp\irb>
</pre>