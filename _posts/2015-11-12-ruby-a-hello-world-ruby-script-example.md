---
ID: 480
post_title: 'Ruby &#8211; A hello world ruby script example'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-a-hello-world-ruby-script-example
published: true
post_date: 2015-11-12 00:00:00
---
<pre>
$ cat testscript.rb
#!/usr/bin/env ruby

a_string = "Hello World!"
puts a_string
puts a_string.class
</pre>

The first line is slightly unusual because:

https://en.wikibooks.org/wiki/Ruby_Programming/Hello_world#Using_env

Next make it an executable:
<pre>
$ chmod 777 testscript.rb
</pre>

then run the script:
<pre>
$ ./testscript.rb
Hello World!
String
</pre>