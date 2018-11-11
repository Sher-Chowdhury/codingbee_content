---
ID: 248
post_title: 'Ruby &#8211; The ERB templating system'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-the-erb-templating-system
published: true
post_date: 2014-12-26 00:00:00
---
The ERB templating system is a really versatile way for dynamically generating customised output from a template. 


It's similar to how php works when it generates web pages, but it is a lot more easier to embed ruby code in ERB files. It has some really powerful intelligence that lets you weave in and out of the template data where necessary.

ruby's ERB template files, are files with the ".erb". These files are basically text files with a bunch of ruby tags embeded in them.  There are a few different types of these ruby tags:

[table id=erb-template-tags /] 



The content that falls outside these tags can be pretty much any data you want, html, xml, plain text....etc. 

You can write .erb files and then test them using the .erb command line utility. 

For example, lets say we have the following erb file, called time.erb:

<pre>
<%# here we display the current time. %>
Hello world. The time is now <%= Time.now %>. Have nice day.
</pre>

Then from the command line, you do:

<pre>
PS C:\Temp\ruby\erb> erb .\time.erb

Hello world. The time is now 2014-12-28 16:48:18 +0000. Have nice day.
</pre>

Note: the leading blank line is caused by the fact that first line starts on the second line. 

Now let's see how to embed a loop into an erb file. Here's a file called simpleloop.erb

<a href="http://codingbee.net/wp-content/uploads/2014/12/sample-html-erb.png"><img src="https://codingbee.net/wp-content/uploads/2014/12/sample-html-erb.png" alt="" width="537" height="257" class="alignnone size-full wp-image-2539" /></a>

Now lets try it:

<a href="http://codingbee.net/wp-content/uploads/2014/12/hnednTb.png"><img src="https://codingbee.net/wp-content/uploads/2014/12/hnednTb.png" alt="" width="385" height="182" class="alignnone size-full wp-image-2540" /></a>

This looks good. Erb noticed that there is a "for" tag and was smart enough to expect an "end" tag later on. When it did encounter the end tag, it managed to recongise that it is the end of the for-loop and every code tags inbetween should be treated as part of that loop. 

We can use <a href="http://codingbee.net/tutorials/powershell/powershell-combine-commands-together-using-pipes/" title="PowerShell - Combine commands together using pipes">Powershell's out-file command</a> to capture the output into an html file:

<pre>
PS C:\Temp\ruby\erb> erb .\simpleloop.erb | Out-File C:\Temp\simpleloop.html
PS C:\Temp\ruby\erb>
</pre>

This will create a new html file, if we open this file we should see:

<a href="http://codingbee.net/wp-content/uploads/2014/12/erb-output1.png"><img src="https://codingbee.net/wp-content/uploads/2014/12/erb-output1.png" alt="" width="277" height="89" class="alignnone size-full wp-image-2536" /></a>


Since the "for" tag and and the "end" tag are single lines, then we could use the "%" syntax instead:


We can also re-write the above erb code in the form of a block, rather than an for-loop:

<a href="http://codingbee.net/wp-content/uploads/2014/12/1wXPBoO.png"><img src="https://codingbee.net/wp-content/uploads/2014/12/1wXPBoO.png" alt="" width="558" height="298" class="alignnone size-full wp-image-2543" /></a>

Notice this time, the start block iterator occupies a few lines, this is possible because the <%...%> tags allows for multi-line ruby code. Once again if you output it:

<a href="http://codingbee.net/wp-content/uploads/2014/12/hnednTb.png"><img src="https://codingbee.net/wp-content/uploads/2014/12/hnednTb.png" alt="" width="385" height="182" class="alignnone size-full wp-image-2540" /></a>


Then capture the output into a html file:

<pre>
PS C:\Temp\ruby\erb> erb .\simpleblock.erb | Out-File C:\Temp\simpleblock.erb
PS C:\Temp\ruby\erb>
</pre>

You'll find that the output is the same again:

<a href="http://codingbee.net/wp-content/uploads/2014/12/erb-output1.png"><img src="https://codingbee.net/wp-content/uploads/2014/12/erb-output1.png" alt="erb-output1" width="277" height="89" class="alignnone size-full wp-image-2536" /></a>