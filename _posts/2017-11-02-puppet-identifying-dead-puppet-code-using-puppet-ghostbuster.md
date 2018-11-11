---
ID: 2400
post_title: 'Puppet &#8211; Identifying dead puppet code using puppet ghostbuster'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-identifying-dead-puppet-code-using-puppet-ghostbuster
published: true
post_date: 2017-11-02 13:57:08
---
This is a how-to guide on using:

https://github.com/camptocamp/puppet-ghostbuster

First get this working so that you can access this gui dashboard:

https://puppet.com/docs/puppetdb/5.1/maintain_and_tune.html#monitor-the-performance-dashboard


Install rvm:

<pre>
$ gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
$ \curl -sSL https://get.rvm.io | bash -s stable --ruby

</pre>

Check rvm install is successful:

<pre>
schowdhury@Shers-MacBook-Pro:~$ rvm --version
rvm 1.29.3 (latest) by Michal Papis, Piotr Kuczynski, Wayne E. Seguin [https://rvm.io]
schowdhury@Shers-MacBook-Pro:~$ rvm list

rvm rubies

=* ruby-2.4.1 [ x86_64 ]

# => - current
# =* - current && default
# * - default

schowdhury@Shers-MacBook-Pro:~$ ruby --version
ruby 2.4.1p111 (2017-03-22 revision 58053) [x86_64-darwin16]

</pre>


Next install the following gems:

<pre>
gem install puppet-ghostbuster
gem install r10k

</pre>


Then clone your repo onto your laptop, and while in this repo, switch to the appropriate branch, then run:

<pre>
$ r10k puppetfile install --verbose
</pre>


then in a seperate bash terminal tab, run:



<pre>$ ssh -L 8080:localhost:8080 {puppetmaster-fqdn}</pre>

Now test this ssh tunnel by opening up a web browser and went to:
http://localhost:8080
 
This is a bit of 'hello world' test . 
 
For reference: https://docs.puppet.com/puppetdb/latest/maintain_and_tune.html#monitor-the-performance-dashboard 


Another test you can do:


<pre>
$ cat ~/identify-obsolete-modules.json
["and",
["=", "environment", "dev"],
["=", "type", "Class"],
["~", "title", "."]]

$ curl -s -G http://localhost:8080/pdb/query/v4/resources --data-urlencode 'pretty=true' --data-urlencode query@identify-obsolete-modules.json | grep title | cut -d'"' -f4 | awk 'BEGIN {FS="::"} {print $1}' | sort | uniq
</pre>


then take a copy of your puppetmaster's hiera.yaml file and place it somewhere on your laptop, then run the following command:

<pre>
$ PUPPETDB_URL='http://localhost:8080/' HIERA_YAML_PATH='/path/to/local/hiera.yaml' find . -type f -exec puppet-lint --only-checks ghostbuster_classes {} \+
</pre>


Here we just used the ghostbuster_classes plugin, but there are all these available too:

https://github.com/camptocamp/puppet-ghostbuster#plugins