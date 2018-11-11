---
ID: 2142
post_title: Using netstat
author: sher
post_excerpt: |
  nmap (aka network mapper) is used to identify what ports are open for a given vm.
layout: post
permalink: >
  https://codingbee.net/tutorials/linux/networking/using-netstat
published: true
post_date: 2017-07-24 22:38:07
---
The netstat (network statistics) command is often used for checking what ports your CentOS machine is listening on. A commonly used command is:

<pre>
 netstat -tulpen
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       User       Inode      PID/Program name
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      0          15449      1/systemd
tcp        0      0 192.168.122.1:53        0.0.0.0:*               LISTEN      0          21357      1971/dnsmasq
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      0          19268      1203/sshd
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN      0          19280      1198/cupsd
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      0          21069      1587/master
tcp6       0      0 :::111                  :::*                    LISTEN      0          15448      1/systemd
tcp6       0      0 :::22                   :::*                    LISTEN      0          19271      1203/sshd
tcp6       0      0 ::1:631                 :::*                    LISTEN      0          19279      1198/cupsd
tcp6       0      0 ::1:25                  :::*                    LISTEN      0          21070      1587/master
udp        0      0 0.0.0.0:25315           0.0.0.0:*                           0          18598      852/dhclient
udp        0      0 0.0.0.0:44262           0.0.0.0:*                           70         15183      675/avahi-daemon: r
udp        0      0 0.0.0.0:5353            0.0.0.0:*                           70         15182      675/avahi-daemon: r
udp        0      0 0.0.0.0:56068           0.0.0.0:*                           0          16853      859/dhclient
udp        0      0 0.0.0.0:21784           0.0.0.0:*                           0          18109      863/dhclient
udp        0      0 127.0.0.1:323           0.0.0.0:*                           997        16117      711/chronyd
udp        0      0 192.168.122.1:53        0.0.0.0:*                           0          21356      1971/dnsmasq
udp        0      0 0.0.0.0:67              0.0.0.0:*                           0          21343      1971/dnsmasq
udp        0      0 0.0.0.0:68              0.0.0.0:*                           0          18643      856/dhclient
udp        0      0 0.0.0.0:68              0.0.0.0:*                           0          18125      863/dhclient
udp        0      0 0.0.0.0:68              0.0.0.0:*                           0          18124      852/dhclient
udp        0      0 0.0.0.0:68              0.0.0.0:*                           0          16889      859/dhclient
udp        0      0 0.0.0.0:59527           0.0.0.0:*                           0          16848      856/dhclient
udp6       0      0 :::25315                :::*                                0          16849      856/dhclient
udp6       0      0 :::56068                :::*                                0          18599      852/dhclient
udp6       0      0 ::1:323                 :::*                                997        16118      711/chronyd
udp6       0      0 :::14349                :::*                                0          18110      863/dhclient
udp6       0      0 :::9748                 :::*                                0          16854      859/dhclient
</pre>

This command shows all states of (t)cp and (u)dp connections that are (l)istening and also provided (e)xtended information about process names, and also show corresponding process (p)id. Also under the 'local address' column, display them as ip and port (n)umbers instead of fqdn and port names. 


The following comman displays the routing table. 

<pre>
$ netstat -nr
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
0.0.0.0         10.0.2.2        0.0.0.0         UG        0 0          0 enp0s3
10.0.2.0        0.0.0.0         255.255.255.0   U         0 0          0 enp0s3
172.28.128.0    0.0.0.0         255.255.255.0   U         0 0          0 enp0s10
192.168.56.0    0.0.0.0         255.255.255.0   U         0 0          0 enp0s9
192.168.56.0    0.0.0.0         255.255.255.0   U         0 0          0 enp0s8
192.168.122.0   0.0.0.0         255.255.255.0   U         0 0          0 virbr0
</pre>