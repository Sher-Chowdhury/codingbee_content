---
ID: 283
post_title: 'RVM &#8211; Deleting a Ruby version'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/rvm-deleting-a-ruby-version
published: true
post_date: 2015-01-25 00:00:00
---
Any gems that you install while using an RVM's ruby version, is self contained in that version. However there may come a time when you no longer want to use a particular ruby version and want to delete it along with all it's gems. Then this can be done using the "remove" command. 


Let's say you have:

<pre>
[sher@puppetmaster ~]$ ruby --version
ruby 2.0.0p598 (2014-11-13 revision 48408) [x86_64-linux]
[sher@puppetmaster ~]$ rvm list

rvm rubies

   ruby-1.8.7-head [ x86_64 ]
   ruby-1.9.3-p551 [ x86_64 ]
=> ruby-2.0.0-p598 [ x86_64 ]
 * ruby-2.2.0 [ x86_64 ]

# => - current
# =* - current && default
#  * - default

[sher@puppetmaster ~]$

</pre>

Now let's say we want to delete version 1.8.7, in that case we simply do:


<pre>
[sher@puppetmaster ~]$ rvm remove 1.8.7
ruby-1.8.7-head - #removing src/ruby-1.8.7-head..
ruby-1.8.7-head - #removing rubies/ruby-1.8.7-head..
ruby-1.8.7-head - #removing gems....
ruby-1.8.7-head - #removing wrappers....
ruby-1.8.7-head - #removing environments....
[sher@puppetmaster ~]$ rvm list

rvm rubies

   ruby-1.9.3-p551 [ x86_64 ]
   ruby-2.0.0-p598 [ x86_64 ]
=* ruby-2.2.0 [ x86_64 ]

# => - current
# =* - current && default
#  * - default

[sher@puppetmaster ~]$
</pre>