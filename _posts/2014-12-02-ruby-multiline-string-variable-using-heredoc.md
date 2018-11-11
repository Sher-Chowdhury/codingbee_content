---
ID: 210
post_title: 'Ruby &#8211; Multiline String Variable (using heredoc)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-multiline-string-variable-using-heredoc
published: true
post_date: 2014-12-02 00:00:00
---
Ruby supports the "heredoc" syntax for strings.  Here's an example:

<pre>
string = <<EOS
  This is the first line.
  This is the second line. 
  This is the third and final line. 
EOS                                  

puts string
</pre>

This outputs:

<pre>
PS C:\Temp\irb> ruby .\heredoc.rb
  This is the first line.
  This is the second line.
  This is the third and final line.
PS C:\Temp\irb>
</pre>

Note, you can't put any spacing before the closing "EOS" terminator, otherwise it will fail. EOS has to start at the very beginning of the line. However you can override this by prefixing "-" at the opening delimiter:

<pre>
string = <<-EOS
  This is the first line.
  This is the second line. 
  This is the third and final line. 
                  EOS

puts string
</pre>

This time I was allowed to indent it. But the output remains the same.