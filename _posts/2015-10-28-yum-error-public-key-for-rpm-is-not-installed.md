---
ID: 473
post_title: 'yum error &#8211; Public key for *.rpm is not installed'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/yum-error-public-key-for-rpm-is-not-installed
published: true
post_date: 2015-10-28 00:00:00
---
the dirty way aroung this is doing:
<pre>$ yum install {package-name} --nogpgcheck</pre>
But best practice is:

first cd to:
<pre>$ cd /etc/pki/rpm-gpg</pre>
Then do a wget command, here's an example:
<pre>$ wget -O http://yum.theforeman.org/releases/1.8/RPM-GPG-KEY-foreman</pre>
then import it into rpm db like this:
<pre>$ rpm --import RPM-GPG-KEY-foreman</pre>
You can list all the imported keys like this:
<pre>$ rpm -qa gpg*</pre>
then to check it have worked, you can do:
<pre>$ rpm -qi gpg-pubkey-225c9b71-54fda121
 Name : gpg-pubkey Relocations: (not relocatable)
 Version : 225c9b71 Vendor: (none)
 Release : 54fda121 Build Date: Wed 28 Oct 2015 11:41:38 AM GMT
 Install Date: Wed 28 Oct 2015 11:41:38 AM GMT Build Host: localhost
 Group : Public Keys Source RPM: (none)
 Size : 0 License: pubkey
 Signature : (none)
 Summary : gpg(Foreman Release Signing Key (1.8) &lt;packages@theforeman.org&gt;)
 Description :
 -----BEGIN PGP PUBLIC KEY BLOCK-----</pre>