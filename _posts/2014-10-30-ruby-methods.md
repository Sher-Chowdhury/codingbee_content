---
ID: 163
post_title: 'Ruby &#8211; Methods'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-methods
published: true
post_date: 2014-10-30 00:00:00
---
In ruby, methods works the same way as c# methods do. 

you can have static and instance methods. 

you define methods using the "def" keyword and construct:


<pre>
def MethodName (OptionalParameters)
   puts OptionalParameter 
end

</pre>

for example if we have:

<pre>
$ cat testscript.rb
#!/usr/bin/env ruby

def WelcomeMessage (a_message)
  puts a_message
end

a_string = "Hello World!"
WelcomeMessage a_string
</pre>

Then we will end up with:

<pre>
$ ./testscript.rb
Hello World!
</pre>

Here, the "puts" command is the ruby equivalent of bash's "echo" command, or powershell's "write-host" command. 

There are a number of other <a href="http://www.tutorialspoint.com/ruby/ruby_builtin_functions.htm">ruby commands</a> that you can use straight from the command line. These commands are available because the <a href="http://www.ruby-doc.org/core-2.1.4/Kernel.html">kernel module</a> is always automatically loaded.  


For example here I have created an .rb file called helloworld2.rb with the following 3 methods:

[ruby]
def simple_welcome
  puts &quot;Hello&quot;
end

puts &quot;the simple_welcome method outputs:&quot;
simple_welcome                            # notice how we call this method like a normal command. 


def normal_welcome (name)

  # If you want to insert a variable inside double-quotes, then you need to
  # encase it in #{...} syntax
  puts &quot;Hello #{name}&quot;
end

puts &quot;the normal_welcome method outputs&quot;

normal_welcome (&quot;CodingBee&quot;)

def complex_welcome (name1, name2, name3)
  puts &quot;Hello #{name1}, #{name2}, and #{name3}&quot;
end

puts &quot;The complex_welcome method outputs&quot;
complex_welcome(&quot;Tom&quot;, &quot;Jerry&quot;, &quot;CodingBee&quot;)   # Note for some reason you have any whitespace before opening bracket.
[/ruby]


The above code outputs:

<pre>
C:\RailsInstaller\Ruby1.9.3\bin\ruby.exe -e $stdout.sync=true;$stderr.sync=true;load($0=ARGV.shift) C:/Users/Mir/RailsProjects/spec2xmlproj/helloworld2.rb
the simple_welcome method outputs
Hello
the normal_welcome method outputs
Hello CodingBee
The complex_welcome method outputs
Hello Tom, Jerry, and CodingBee
</pre>


Any variables set in a method will be confined to that method's scope. Also a method can't access any variables outside it's scope. e.g.:


<pre>
website_name = "codingbee"
puts website_name

def normal_welcome
  # puts website_name           # this will output an error message, hence commented out.  
  website_name = "google"
  puts website_name
end

normal_welcome

puts website_name
</pre>

This outputs:

<pre>
PS C:\Temp\irb> ruby .\scope.rb
codingbee
google
codingbee
PS C:\Temp\irb>
</pre>

If you want a variable accessible/settable everywhere, then you need to create a "global variable". global variables are prefixed with the dollar symbol, e.g.:

[ruby]
$website_name = 'codingbee'
puts $website_name

def normal_welcome

  puts $website_name
  $website_name = 'google'
end

normal_welcome

puts $website_name
[/ruby]


The above outputs:

<pre>
$ ./testscript.rb
codingbee
codingbee
google
</pre>


Note: if you a method to output an object instead of using "puts" output, then simply specify the object as the last line in the method.