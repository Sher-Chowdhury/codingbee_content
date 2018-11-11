---
ID: 342
post_title: 'RHCSA &#8211; SSH fingerprint authentication'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-ssh-fingerprint-authentication
published: true
post_date: 2015-04-04 00:00:00
---
When you ssh to a linux machine, the first thing the remote machine does is repond by sending a fingerprint id to the requester:


<pre>
[sher@localhost .ssh]$ ssh localhost
The authenticity of host 'localhost (::1)' can't be established.
ECDSA key fingerprint is d7:2b:04:af:6f:29:72:7a:56:b7:de:80:b5:6a:e7:9c.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'localhost' (ECDSA) to the list of known hosts.
sher@localhost's password:
Last login: Sat Apr  4 02:09:43 2015 from powershellpc.codingbee.dyndns.org
[sher@localhost ~]$

</pre>


If this finger print id is not already listed in your local ~/.ssh/known_hosts file, then it will ask you to confirm that you trust the remote machine. Once you confirm it, the fingerprint will then get appended to the known_hosts file:   

<pre>
[sher@localhost .ssh]$ cat known_hosts
localhost ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBMNtomP4wf3baK/8nxXCYbIJxHxdDKPXHomYgYqMfQOuTV//LoVM6EXvhyZgpgebCupx+jDalnivqmtVM3kqy7A=
[sher@localhost .ssh]$

</pre>

Note this string is taken from the following file on the remote machine:


<pre>
$ cat /etc/ssh/ssh_host_ecdsa_key.pub
ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBMNtomP4wf3baK/8nxXCYbIJxHxdDKPXHomYgYqMfQOuTV//LoVM6EXvhyZgpgebCupx+jDalnivqmtVM3kqy7A=
</pre>

After that has happened, if you disconnect and then ssh back in again, then the remote machine will send the fingerprint again and this time your local machine will automatically trust the the target machine since there is an encrypted trust token in the known_hosts file for this fqdn/ip-number/hostname. Notice that the known_host file resides in the user's home directory. That means if you try to remotely login again (using the same remote credentials) but this time while logged in as a different user, then you'll need to to another 

However if the target machine has been rebuilt (i.e. has it's os reinstalled), but still retains the same ip-number and fqdn, then the rebuilt target machine will this time issue a different fingerprint id for any new incoming ssh requests. This time when you try to ssh into the machine you will get a warning message, indicating a possible man-in-the-middle attack.