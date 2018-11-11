---
ID: 582
post_title: 'Python &#8211; Pip'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/python/python-pip
published: true
post_date: 2016-05-11 00:00:00
---
https://pip.pypa.io/en/stable/

To <a href="https://packaging.python.org/en/latest/install_requirements_linux/#centos-rhel">install python on centos</a>:

<pre>
$ yum install python-pip</pre>

Some useful commands:

<pre>
$ pip list    # equivalent to: yum list aailable ...

$ pip show {packagename}  # equivalent to: yum info ....   

$ pip search {keyworld}   # equivalent to: yum search ....
</pre>


https://pypi.python.org/pypi     # equivalent to rubygems.org


In Ruby, we hae the bundler gem, in python you can achiee the same result by running:

<pre>
$ pip freeze > requirements.txt
$ pip install -r requirements.txt

</pre>