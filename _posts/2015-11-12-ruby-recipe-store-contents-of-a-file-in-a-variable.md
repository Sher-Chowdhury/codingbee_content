---
ID: 481
post_title: 'Ruby Recipe &#8211; Store contents of a file in a variable'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-recipe-store-contents-of-a-file-in-a-variable
published: true
post_date: 2015-11-12 00:00:00
---



<pre>
$ cat testscript.rb
#!/usr/bin/env ruby

data = File.read("/tmp/testfile.txt")
puts data
$
$ cat /tmp/testfile.txt
Hello!
here is some content.
Goodbye.
$
$ ./testscript.rb
Hello!
here is some content.
Goodbye.
$
</pre>