---
ID: 2635
post_title: 'How to fix the &#8220;No manual entry for {command}&#8221; man error'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/cookbooks/centos/man-how-to-fix-the-no-manual-entry-for
published: true
post_date: 2018-02-28 13:52:15
---
Sometimes you will encounter an error like this when trying to use man:

<pre>
$ man vmstat
No manual entry for vmstat
</pre>


To fix this, first check that you have the following rpms installed:


<pre>
$ yum install -y man-pages
$ yum install -y man-db
</pre>

Next run the mandb command to populate/update the mandb database:


<pre>
$ mandb
</pre>

Then run the following command, to try to find the man .gz man file. 

<pre>
$ yum whatprovides */vmstat.*.gz
</pre>
In your case you replace 'vmstat' with whatever command that you're having problems with. 

Now if that packages isn't installed, then install it. in my case it was already installed, but the file itself was missing for some reason:

<pre>
$ file /usr/share/man/man8/vmstat.8.gz
/usr/share/man/man8/vmstat.8.gz: cannot open (No such file or directory)
</pre>

In this situation, just reinstall the rpm again:


<pre>
$ yum reinstall procps
</pre>