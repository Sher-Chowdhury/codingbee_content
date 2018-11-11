---
ID: 334
post_title: 'Linux &#8211; rhn-channel and Spacewalk'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/linux-rhn-channel-and-spacewalk
published: true
post_date: 2015-03-31 00:00:00
---
The Spacewalk server is your custom rpm server which hosts all your rpm packages. If you want to connect to an Spacewalk server, you need to viers install the following package:

<pre>
$ yum install rhn-setup
</pre> 


https://access.redhat.com/documentation/en-US/Red_Hat_Network_Satellite/5.5/html/Channel_Management_Guide/chap-Red_Hat_Network_Satellite-Channel_Management_Guide-Introduction_to_RHN_Channels.html 


http://www.spacewalkproject.org/

the following lists all channels a machine is subscribed to:
<pre># rhn-channel --list
</pre>

Alternatively do:

<pre>
$ spacewalk-channel --list
</pre>




to register a client to a spacewalk server, you use the <a href="http://linux.die.net/man/8/rhnreg_ks">rhnreg_ks</a> command:


<pre>
$ rhnreg_ks --activationkey {channel name}
</pre>



To subscribe to a new channel, we do:

https://access.redhat.com/solutions/57504 

See also:

https://access.redhat.com/solutions/57504


also run the following command to check what repos you're connected to:

<pre>
$ yum repolist
</pre>