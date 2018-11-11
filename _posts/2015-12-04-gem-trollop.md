---
ID: 493
post_title: 'gem &#8211; trollop'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rubygems/gem-trollop
published: true
post_date: 2015-12-04 00:00:00
---
trollop lets you easily create script parameters with meaningful names, instead of useing ARGV[0], ARGV[1],...etc. 

If you have the following in your ruby script:


<pre>
cat ./testscript.rb
#!/usr/bin/env ruby

require 'trollop'

opts = Trollop::options do
  opt :hostname, "Server FQDN.", :type => :string
end

puts opts[:hostname]

</pre>

Next we install the gem:


<pre>$ gem install trollop</pre>

Now we have builtin "help" feature:


<pre>$ ruby ./testscript.rb --help
Options:
  -h, --hostname=<s>    Server FQDN.
  -e, --help            Show this message</pre>

Now if we do:

<pre>
$ ruby ./testscript.rb --hostname=test.com
test.com
</pre>