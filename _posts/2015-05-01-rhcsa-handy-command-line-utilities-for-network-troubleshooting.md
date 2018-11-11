---
ID: 368
post_title: 'RHCSA &#8211; Handy command line utilities for network troubleshooting'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-handy-command-line-utilities-for-network-troubleshooting
published: true
post_date: 2015-05-01 00:00:00
---
Here are some useful commands to help you locate and resolve network related issues:


<ul>
	<li><strong>ping</strong> - tests connectivity</li>
	<li><strong>traceroute</strong> - gives routing info. I.e. it gives you a list of all devices a data packet went throught to reach it's destination. However this command isn't that useful anymore because a lot of routers are locked down and doesn't provide any info. I.e. there is filtering on the routers. When this occurs, each row is displayed as "* * *"</li>
	<li><strong>tracepath </strong>- alternative to traceroute</li>
	<li><strong>dig </strong>- gets DNS information</li>
	<li><strong>nmap </strong>- This is useful for troubleshooting network related issues by showing you what ports are opening and the corresponding services listening in on them. Be careful you have permission of the target machine owner before running this command against it.</li>
	<li><strong>ss -atn</strong> - this is a better version of the netstat command "netstat". </li>

</ul>







<h2>Troubleshooting approach</h2>

Here's a good approach to review your network settings in order to find any faults:

<ol>
	<li>find out your machines ip address - do this using <code>ip addr show</code></li>
	<li>Check that default route is set, and default gateway ip is in the same range as your machine's ip address. You can do this using <code>ip route show</code></li>
	<li>Check what is the dns ip address that is currently being used. Best way to do this is <code>cat /etc/resolv.conf | grep "nameserver"</code></li>
	<li>now use ping to see if you can ping to the default gateway, i.e. default router</li>
	<li>now ping the dns</li>
	<li>now use the host command, i.e. <code>host {fqdn}</code>. This will return the fqdn's ip address. This will test that dns's host name resolution is working as expected. However if /etc/hosts already has a matching entry, then the host command will still return a positive result, even if the dns server is switched off. Note the windows equivalent to this command is nslookup.</li>
	<li>using dig command, i.e. <code>dig {fqdn}</code>, this gives more detailed info and it will specifically test the dns server, and ignore the local /etc/hosts file. If everything is ok, then this command will  output "NOERROR" somewhere within the output. If however if gives "NXDOMAIN", then that means that the domain doesn't exist.</li>



Here's another troubleshooting approach:


Here is a good network troubleshooting technique using ping. Do the following in sequence to test connectivity in small steps that gradually gets bigger:
<ol>
	<li>                 ping localhost      # This checks machine’s internal networking software is working correctly. “localhost” is an alias in /etc/hosts file. If sucsseful then do:</li>
	<li>                 ping eth0                                                                  # check connection to the eth0 (networking card hardware) interface. If successful, then do:</li>
	<li>                 ping {default gateway ip}                    # pings to the local router</li>
	<li>                 ping {another device on same network}                            #</li>
	<li>                 ping {external ip address}                # If it fails then it means that there is a breakdown in the connection somewhere between our router and destination server.</li>
</ol>