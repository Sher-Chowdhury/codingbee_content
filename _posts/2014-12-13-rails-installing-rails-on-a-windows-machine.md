---
ID: 218
post_title: 'Rails &#8211; Installing Rails on a Windows Machine'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rails/rails-installing-rails-on-a-windows-machine
published: true
post_date: 2014-12-13 00:00:00
---
Rails (aka ruby on rails), is a collection of ruby related products. 

On windows, it is possible to install Ruby, and install rails separately, however it is much more trouble-free if you install them together using the <a href="http://railsinstaller.org/en">railsinstaller</a>.    


First install:

http://railsinstaller.org/en

You might also need to add the following variables to the path variable:

<pre>
C:\RailsInstaller\Ruby2.1.0
C:\RailsInstaller\Ruby2.1.0\bin    </pre>

Note, the ruby version numbwers could be slightly different, so first locate your bin directory and ensures that it exists before adding it tot the path variable. 

This bin, directory above has a number of essential <a href="http://codingbee.net/tutorials/ruby/ruby-command-line-utilities/" title="Ruby - Command Line Utilities">ruby/rails command line utilities</a>. 

Now open up a powershell terminal and check what version of the tools we have installed:


<pre>
PS C:\Temp\rails> ruby -v
ruby 2.1.5p273 (2014-11-13 revision 48405) [i386-mingw32]
PS C:\Temp\rails> rails -v
DL is deprecated, please use Fiddle
Rails 4.1.8
PS C:\Temp\rails> bundler -v
DL is deprecated, please use Fiddle
Bundler version 1.7.7
PS C:\Temp\rails> gem -v
2.2.2
PS C:\Temp\rails> irb -v
DL is deprecated, please use Fiddle
irb 0.9.6(09/06/30)
PS C:\Temp\rails> rake --version
rake, version 10.4.2
</pre>

This also is a way to check that all the tools have been installed and are responding. 



<h2>Troubleshooting</h2>



Also, follow this guide if you have matching error message:

https://gist.github.com/fnichol/867550

if you are following above troubleshooting instructions about ssl certificate, then to make it permenant, you simply create a new  sytem environment variable, i.e:

<a href="http://codingbee.net/wp-content/uploads/2014/12/system-environment-variable.png"><img src="http://codingbee.net/wp-content/uploads/2014/12/system-environment-variable.png" alt="" width="1233" height="712" class="alignnone size-full wp-image-2211" /></a>