---
ID: 3779
post_title: RHCE – rough notes
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/rhce-rough-notes
published: true
post_date: 2018-06-19 20:50:57
---
page 4:

systemctl --type=service


list all running services:

systemctl --list-units --type=service

list all services, running and not running:

systemctl --list-units --type=service


page 6:

lists the units that this unit depends on:

 systemctl list-dependencies UNIT

shows what other services depends on the following unit:
systemctl list-dependencies UNIT --reverse


this forcefully disables a unit, so that it doesn't start up at boot time or manually started. this  
is so to prevent accidentally starting a unit. e.g. don't accidentally start chronyd if already using ntpd
systemctl mask unit


Opposite of umask:

systemctl umask unit


page 26

the following command is best practice because it is persistent:

nmcli dev dis device

and don't use (because it isn't persistent):

nmcli connection down device


page 27:

The following set's static ip address 192.0.5.20/24 as well as default gateway address of 192.0.5.254:

nmcli con mod static-eth0 ipv4.addresses "192.0.5.20/24 192.0.5.254"

note: you can omit the default gateway address. 






page 28 


this adds dns entry to a connection:

nmcli con mod static-eth0 +ipv4.dns 192.0.2.1

the '+' means you a connection have multiple dns entries for redundancy purposes. 



to update /etc/resolv.conf with the new nameserver entries. The nmcli mod sub command only edits config files. they don't load in the changes. that's why we need
to restart the connection to load in the changes:

nmcli con down static-eth0
nmcli con up static-eth0

note /etc/resolv.conf stores multiple nameserver entries, which is the sum taken from each interfaces connections. 




heres a breakdown of all the nmcli settings:

man nm-settings

the namings don't match up with ifcfg-* files because of backward compatibility reasons. 



page 29

set hostname using hostnamectl command (covered in rhcsa)




page 35 - ipv6

at the moment there's no easy way for ipv4 only interfaces to communicate with ipv6 only interfaces. as a work around, interfaces are assigned with both ipv4 and ipv6 
addresses. This workaround is called dual-stack configuration 


each ipv6 address is made up of 8 colon separated groups, called nibbles. each nibble is made up of 4 characters, although leading 
zeros can be omitted. 

page 36  


eg. the following:

2001:0db8:0000:0010:0000:0000:0000:0001

can be written in more shorthand form to:


2001:db8:0:10:0:0:0:1

which can be shortened even further to:


2001:db8:0:10::1


note, the "::" this is used for shorthanding 2 or more consecutive all-zero nibbles. 

Note, you can only have one "::" reference you would start to introduced ambiguity. Also for one all-zero nibble, it's advised to leave
them as ':0:'.

ipv6 should never contain upper case letters. 

using the following notation if you want to incldue a port number, e.g. port 80:

[2001:db8:0:10::1]:80


ipv4 addresses are made up of 32 bits. However
ipv6 addresses is made up of 128 bits.

for ipv4 it is mandatory to explicitly provide a subnet mask prefix, e.g. 16, 24, etc. 

However for ipv6 it is optional since there is a default of 64. 


An internet service provide (ISP) can provide a massive range of public ipv6 addresses to big orgnaisations (e.g AWS) such as /48 (wich in itself is more than ipv4). This range can 
be further subdivided by aws to smaller ranges to help keep things organised. 

/48 means you can creates about 65000 subnets alone. aws can use e.g. 16 bits for it's own internal subnetting. 

ipv6 equivalents to ipv4:


::1/128       this is equivalent to      127.0.0.1/8     (run 'ip a' to view this)
::       this is equivalent to      0.0.0.0     (i.e. any ip addresses) 
::/0       this is equivalent to      0.0.0.0/0   (this is often used in route tables as a catchall rule)
fd00::/8       this is equivalent to      private range, e.g. 192.168.0.0/16 


::1/128       this is equivalent to      127.0.0.1/8 
::1/128       this is equivalent to      127.0.0.1/8 
::1/128       this is equivalent to      127.0.0.1/8 



Link local addresses allow machines to automatically have an IP address on a network if they haven't been manually configured or automatically configured by a special server on the network (DHCP). Before an address is chosen from that range, the machine sends out a special message (using ARP which stands for address resolution protocol) to the machines on the network around it (assuming that they also haven't been assigned an address manually or automatically) to find out if 169.254.1.1 is free. If it is, then the machine assigns that address to its network card. If that address is already in use by another machine on the same network, then it tries the next IP 169.254.1.2 and so on, until it finds a free address.

https://serverfault.com/questions/118324/what-is-a-link-local-address/118329#118329