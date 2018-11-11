---
ID: 130
post_title: >
  Enable-psremoting while Virtualbox is
  installed
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/enable-psremoting-while-virtualbox-is-installed
published: true
post_date: 2014-07-27 00:00:00
---
If you have virtualbox installed on your machine, then as a side effect, you'll discover that the enable-psremoting no longer works. That's because as part of the vb install, it created a "public network" adapter as a byproduct, which blocks enable-psremoting from working. This is actually a bug relating to virtualbox and can be fixed by adding the following entry in regedit:

<a href="http://codingbee.net/wp-content/uploads/2014/07/tEp6nCh.png"><img class="alignnone size-full wp-image-1252" src="http://codingbee.net/wp-content/uploads/2014/07/tEp6nCh.png" alt="" width="1171" height="528" /></a>

&nbsp;

1. Open regedit and navigate to <em>HKLM\SYSTEM\CurrentControlSet\Control\Class\{4D36E972-E325-11CE-BFC1-08002BE10318}</em>

2. Browse through the subkeys (named 0000, 0001, etc) until you find the subkey containing the virtualbox network adapter, this is the one where the “<em>DriverDesc</em>” key has “<em>VirtualBox Host-Only Ethernet Adapter</em>” as value.

3. Add a new DWORD value with a name of “<em>*NdisDeviceType</em>” and a value of “<em>1</em>”

&nbsp;

&nbsp;

http://www.brokenwire.net/bw/Various/120/fix-virtualbox-causes-unidentified-network-on-vista-and-windows-7