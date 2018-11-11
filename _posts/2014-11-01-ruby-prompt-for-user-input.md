---
ID: 170
post_title: 'Ruby &#8211; Prompt for user input'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-prompt-for-user-input
published: true
post_date: 2014-11-01 00:00:00
---
In Linux you can prompt a user provide input by using the "read" command. You can do the same thing with Powershell by using the read-host command. 

Similarly you can do the same thing in Ruby using the "gets" command:


[ruby]
print &quot;Please enter your name: &quot;
name = gets
print &quot;Welcome #{name.strip}!!!&quot;
[/ruby]

Note: I used the strip method to remove any whitespacing. 

The above should output the following (assuming you enter "CodingBee" when prompted):

<pre>
C:\RailsInstaller\Ruby1.9.3\bin\ruby.exe -e $stdout.sync=true;$stderr.sync=true;load($0=ARGV.shift) C:/Users/Mir/RailsProjects/spec2xmlproj/helloworld2.rb
Please enter your name: Sher
Welcome Sher!!!
Process finished with exit code 0
</pre>