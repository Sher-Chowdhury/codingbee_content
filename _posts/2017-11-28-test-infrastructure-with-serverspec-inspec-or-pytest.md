---
ID: 2418
post_title: >
  test infrastructure with serverspec,
  inspec, or pytest
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/test-infrastructure-with-serverspec-inspec-or-pytest
published: true
post_date: 2017-11-28 20:36:14
---
Here's a simple hello world pytest example to run on a centos box:

<pre>
$ cat test_sample.py
import commands
status, outputx = commands.getstatusoutput("yum info tree | grep '^Repo' | grep -o 'installed'")
def test_answer():
    assert outputx == 'installed'
</pre>