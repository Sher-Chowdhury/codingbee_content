---
ID: 444
post_title: 'Foreman &#8211; triggering a puppet run from the gui interface'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/foreman-triggering-a-puppet-run-from-the-gui-interface
published: true
post_date: 2015-07-27 00:00:00
---
https://github.com/ripienaar/mcollective-vagrant







On the agent, add the following near to the top of the file:

<pre>
# vim /etc/puppet/auth.conf
path /run
allow *
</pre>

Note: later on change the "*" to the fqdn of the puppetmaster fqdn. 


On the puppetmaster enable mcollective:



<pre>
$ cat /etc/foreman-proxy/settings.d/puppet.yml
.
.
.# valid providers:
#   puppetrun   (for puppetrun/kick, deprecated in Puppet 3)
#   mcollective (uses mco puppet)
#   puppetssh   (run puppet over ssh)
#   salt        (uses salt puppet.run)
#   customrun   (calls a custom command with args)
:puppet_provider: mcollective
.
.
.
</pre>





Foreman gui:
on foreman settings (More -> Settings -> “Puppet”tab), set puppetrun to “true”.

Edit the master sudoers file as described here:

http://theforeman.org/manuals/1.8/index.html#4.3.7Puppet


On the agent add the following to sudoers file:

<pre>
# /etc/sudoers
foreman-proxy ALL=(ALL) NOPASSWD: ALL
foreman ALL=(ALL) NOPASSWD: ALL


</pre>


On foreman, You might need to do the following for the first time only:

<pre>
# on the agent first do:
$ service puppet restart
# then on master, do:
puppet kick puppetagent01.local
# then on foreman gui, hit the puppet run button
</pre>


https://tickets.puppetlabs.com/browse/PUP-2659 

http://wiki.infn.it/progetti/cloud-areapd/best_practices/config_puppetrun

https://groups.google.com/forum/#!topic/foreman-users/vFViUPc8zok

http://serverfault.com/questions/638360/how-to-get-run-puppet-button-working-on-foreman


This might be best:
http://projects.theforeman.org/projects/foreman/wiki/_mcollective_






https://github.com/witlessbird/foreman_mco    # you can install this using:
 
<pre>
$ yum install ruby193-rubygem-foreman-mco.noarch
</pre>

I think on the agents you need to do:


<pre>
$ yum install mcollective-client
</pre>

Actually, I think the above is done on the foreman server itself, see:

http://www.pythian.com/blog/some-observations-of-puppetrun-with-foreman/ 

https://github.com/puppet-community/puppet-mcollective