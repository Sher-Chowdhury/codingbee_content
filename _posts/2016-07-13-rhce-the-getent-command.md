---
ID: 585
post_title: The getent command
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/cookbooks/centos/rhce-the-getent-command
published: true
post_date: 2016-07-13 00:00:00
---
The getent command essentially queries a collection of sources to return a comprehensive list. 

<pre>
$ get passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-bus-proxy:x:999:998:systemd Bus Proxy:/:/sbin/nologin
systemd-network:x:998:997:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:997:996:User for polkitd:/:/sbin/nologin
sssd:x:996:995:User for sssd:/:/sbin/nologin
abrt:x:173:173::/etc/abrt:/sbin/nologin
.
.
...etc
</pre>


The above is a combined list of local accounts (i.e. those listed in /etc/hosts file) along with all LDAP/AD accounts, if this server is connected to an AD/LDAP server. Hence the getent's "passwd" db is combined listing from 2 sources. It is these two sources because:


<pre>
$ cat /etc/nsswitch.conf | grep '^passwd'
passwd:     files sss ldap
</pre>  

Here it indicates when looking for available first looking in /etc/passwd (files) followed by the System Security Subsystem (sss), which I think is depracated, and finally ldap. Note by default, ldap isn't present here, so I manually added that in.