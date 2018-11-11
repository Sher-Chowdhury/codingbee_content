---
ID: 433
post_title: 'Tip: avoid redownloading after each rspec run'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rspec-puppet/tip-avoid-redownloading-after-each-rspec-run
published: true
post_date: 2015-06-24 00:00:00
---
the rake's spec command does is comprised of the following:

spec = spec_prep + spec_standalone + spec_clean

So we need to avoid running spec_clean. To do this simply run:

<pre>$ bundle exec rake spec_prep</pre>

After that, always run


<pre>$ bundle exec rake spec_standalone</pre>

Instead of the "spec" command