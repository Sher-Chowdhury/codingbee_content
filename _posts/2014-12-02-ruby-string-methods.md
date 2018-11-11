---
ID: 212
post_title: 'Ruby &#8211; String Methods'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-string-methods
published: true
post_date: 2014-12-02 00:00:00
---
<pre>
irb(main):003:0> message = "hello"
=> "hello"
irb(main):004:0> message.upcase
=> "HELLO"
irb(main):005:0> puts message
hello
=> nil
irb(main):006:0> message.upcase!
=> "HELLO"
irb(main):007:0> puts message
HELLO
=> nil
irb(main):008:0> message.downcase!
=> "hello"
irb(main):013:0> greetings = "                    hello world!           "
=> "                    hello world!           "
irb(main):014:0> puts greetings
                    hello world!
=> nil
irb(main):015:0> greetings.strip!.capitalize!        # here we are chaining methods. 
=> "Hello world!"
irb(main):016:0> puts greetings
Hello world!
=> nil
irb(main):017:0>



irb(main):061:0* htmlgreeting = "[html][body][strong]hello world[/strong][/body][/html]"
=> "[html][body][strong]hello world[/strong][/body][/html]"
irb(main):062:0> puts htmlgreeting
[html][body][strong]hello world[/strong][/body][/html]
=> nil
irb(main):063:0> puts htmlgreeting.gsub("[","<").gsub("]",">")
<html><body><strong>hello world</strong></body></html>
=> nil
irb(main):064:0>



irb(main):064:0> weekdays = "monday->tuesday->wednesday->thursday->friday"
=> "monday->tuesday->wednesday->thursday->friday"
irb(main):065:0> array_weekdays = weekdays.split("->")
=> ["monday", "tuesday", "wednesday", "thursday", "friday"]
irb(main):066:0> puts array_weekdays[2]
wednesday
=> nil
irb(main):067:0>


</pre>