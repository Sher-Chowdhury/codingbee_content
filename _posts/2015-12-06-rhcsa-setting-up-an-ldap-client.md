---
ID: 495
post_title: Setting up an LDAP client
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-setting-up-an-ldap-client
published: true
post_date: 2015-12-06 00:00:00
---
<h2>Overview</h2>
There are 2 main server-side ldap software you can use to set up an ldap server:
<ul>
 	<li>OpenLDAP</li>
 	<li>Microsoft Active Directory (AD)</li>
</ul>
Unfortunately we don't have a single client-side ldap software that can connect to both of these types of LDAP servers. Instead we have the following 2 options:
<ul>
 	<li><strong>openldap-clients</strong>: This ldap client-side software is used for connecting to an OpenLDAP based LDAP server</li>
 	<li><strong>realmd</strong>: This ldap client-side software is used for connecting to an Microsoft AD based LDAP server</li>
</ul>
In most cases you won't know which type of LDAP server you are dealing with. If that is the case, then the only way to do figure it out is by first try one approach, if that doesn't work then try the other approach.

One of the RHCSA exam object is:
<blockquote>Configure a system to use an existing authentication service for user and group information</blockquote>
You don't need to know how to set up openldap server itself, however you do need an an existing dummy openldap server as a target machine to try connecting to from your CentOS7 machine.

In that respect you do need to set up a dummy openldap server, which is quite complicate and a pain. However the good news is that I have created a complete openldap sandpit environment in the form of a vagrant project:

<a href="https://github.com/Sher-Chowdhury/vagrant-openldap">https://github.com/Sher-Chowdhury/vagrant-openldap</a>

If you don't know what <a href="https://www.vagrantup.com/docs/">vagrant</a> is then I strongly recommend reading up about it. Vagrant isn't something you need to know about for the RHCSA exam, but is popular tool to become familiar with as it's a massive time saver in the long run. Also you only need basic familiarity with vagrant to start using this vagrant project.
<h2>Pre-reqs</h2>
In order to setup your machine as an openldap client, you need to have the following:
<ul>
 	<li>openldap server's fqdn and "base dn". They look something like this:Base DN: dc=codingbee,dc=net
fqdn: ldap://ldap.codingbee.netAs you can see, if you did end up with one of the above info, you can derive the other without too much difficulty.</li>
 	<li>.pem file</li>
</ul>
<h2>Setting up LDAP client (authconfig approach)</h2>
Let's assume we have a openldap based ldap server. In that case we first install:
<pre>$ yum install -y openldap-clients nss-pam-ldapd nss_ldap authconfig-gtk</pre>
Once authconfig-gtk is installed, start the gui interface like this:
<pre>$ authconfig-gtk
</pre>
A window should now pop up:

<a href="http://codingbee.net/wp-content/uploads/2015/04/g5oQadP.png"><img class="alignnone size-full wp-image-3725" src="http://codingbee.net/wp-content/uploads/2015/04/g5oQadP.png" alt="" width="493" height="556" /></a>

Then select ldap from the dropdown list and you should then see:

<a href="http://codingbee.net/wp-content/uploads/2015/04/5VdZJKb.png"><img class="alignnone size-full wp-image-3726" src="http://codingbee.net/wp-content/uploads/2015/04/5VdZJKb.png" alt="" width="487" height="726" /></a>

The select "ldap password" from the authentication method drop down list:

<a href="http://codingbee.net/wp-content/uploads/2015/04/3Hy5Kaf.png"><img class="alignnone size-full wp-image-3727" src="http://codingbee.net/wp-content/uploads/2015/04/3Hy5Kaf.png" alt="" width="491" height="555" /></a>

Now fill in the <em>LDAP Search Base DN</em> and <em>ldap server</em> fields. Both these info will be provided in the rhcsa exam. However it may be in the form a single piece of info, e.g you are just given in the "ldap server" info:
<pre>Next you need to check the tls certificate checkbox, if you are given the location of a .pem file.</pre>
Then in the download cert box, enter the location of the certificate, e.g.:
<pre>ftp://certs.codingbee.net/resources/cert.pem
</pre>
Finally click "apply" and then wait a few minutes.

after a few minutes the window closes and you end up back at the terminal.

Then finally we restart sshd service:
<pre>$ systemctl restart sshd
</pre>
<h2>Setting up LDAP client (realmd approach)</h2>
We can configure all other Linux servers to authenticate user login credentials against this LDAP server. This kind of setup is known as "single sign-on", aka SSO. This is one of the main purposes of using ldap.

For the RHCSA and RHCE exam, you only need to know how to set up your Linux machine as an LDAP client that can connect to an existing LDAP server. You don't need to know how to setup the LDAP server itself.

There's 2 ways to configure an ldap client, they are <strong>realmd</strong> and <strong>authconfig-gtk</strong>. In this article we will take a look at the realmd approach.

First off, in order for the realmd ldap client to work, the ldap software running on the ldap server needs to be realmd compatible. The only way to find out is by running the "realmd discover", which you can only do part way through setting up the realmd cleint software.

Let's say our ldap server is:
<pre>puppetagent01.local 
</pre>
Let's first see if we can ping to it:
<pre>$ ping -c 3 puppetagent01.local
PING puppetagent01 (192.168.50.11) 56(84) bytes of data.
64 bytes from puppetagent01 (192.168.50.11): icmp_seq=1 ttl=64 time=0.412 ms
64 bytes from puppetagent01 (192.168.50.11): icmp_seq=2 ttl=64 time=0.728 ms
64 bytes from puppetagent01 (192.168.50.11): icmp_seq=3 ttl=64 time=0.825 ms

--- puppetagent01 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2006ms
rtt min/avg/max/mdev = 0.412/0.655/0.825/0.176 ms

</pre>
Now install the realmd package:
<pre>$ yum install -y realmd
</pre>
This package is able to discover realmd based LDAP servers. Once we have info we can then try to do a handshake to the LDAP server like this:
<pre>$ realm discover puppetagent01.local
See: journalctl REALMD_OPERATION=r4373.10347
realm: No such realm found: puppetagent01.local
</pre>
Here it didn't work, this is most likely because the ldap server's ldap software doesn't support realmd type connections. In this situation, you need to set up the ldap client using authconfig-gtk instead of realmd.

####### the rest of this article will need more work.

Assuming the above worked, we can continue.

When you run this command, you might get prompted to install some other package. In which install those as you well using yum.

Next we "join" the ldap server:
<pre>$ realm join ldap.codingbee.net
Password for Administrator: 
</pre>
Here you'll get prompted to enter the password.

To confirm this has worked, we simply run the discover command again:
<pre>$ realm discover ldap.codingbee.net
</pre>
This time it will give more info indicating that you have successfully connected to the server. It will also indicate the type of ldap server, this is usually "kerberos"

You may also need to configure the <code>/etc/ssh/sshd_config</code>. Here you just find all the kerberos setting, enable them, and then set them to 'yes'.
<pre>$ cat /etc/ssh/sshd_config | grep Kerberos
# Kerberos options
#KerberosAuthentication no
#KerberosOrLocalPasswd yes
#KerberosTicketCleanup yes
#KerberosGetAFSToken no
#KerberosUseKuserok yes
</pre>
Also don't forget to restart sshd:
<pre>$ systemctl restart sshd</pre>
&nbsp;

&nbsp;
<h2>The login binary (extra/background reading)</h2>
When a user attempts to login to a vm, they get prompted to enter a username followed by the password. This login/password prompting feature is provided by the login binary:
<pre>$ which login
/usr/bin/login
$ file /usr/bin/login
/usr/bin/login: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.32, BuildID[sha1]=0xb64a9cff8ba34aa4586e27a77909ef8fbc4a9769, stripped
</pre>
This binary has dependencies on the following shared libraries:
<pre>[root@localhost ~]# ldd /usr/bin/login
        linux-vdso.so.1 =&gt;  (0x00007fff091fe000)
        libpam.so.0 =&gt; /lib64/libpam.so.0 (0x00007fbf0de08000)
        libpam_misc.so.0 =&gt; /lib64/libpam_misc.so.0 (0x00007fbf0dc04000)
        libaudit.so.1 =&gt; /lib64/libaudit.so.1 (0x00007fbf0d9dc000)
        libselinux.so.1 =&gt; /lib64/libselinux.so.1 (0x00007fbf0d7b7000)
        libc.so.6 =&gt; /lib64/libc.so.6 (0x00007fbf0d3f6000)
        libdl.so.2 =&gt; /lib64/libdl.so.2 (0x00007fbf0d1f1000)
        libpcre.so.1 =&gt; /lib64/libpcre.so.1 (0x00007fbf0cf90000)
        liblzma.so.5 =&gt; /lib64/liblzma.so.5 (0x00007fbf0cd6b000)
        /lib64/ld-linux-x86-64.so.2 (0x00007fbf0e02a000)
        libpthread.so.0 =&gt; /lib64/libpthread.so.0 (0x00007fbf0cb4e000)

</pre>
You can think of all of these libraries as plugins that adds extra functionality to the login tool.

The "libpam.so.0" is listed second here, which is part of Pluggable Authentication Module (pam).

All of pam's config files are listed under the following directory:
<pre>$ [root@localhost ~]# ls -l /etc/pam.d/
total 156
-rw-r--r--. 1 root root  272 Oct  7  2014 atd
-rw-r--r--. 1 root root  192 Mar  6 05:58 chfn
-rw-r--r--. 1 root root  192 Mar  6 05:58 chsh
-rw-r--r--. 1 root root  232 Mar  6 04:55 config-util
-rw-r--r--. 1 root root  293 Jul 30  2014 crond
-r--r--r--. 1 root root  146 Mar  5 22:51 cups
lrwxrwxrwx. 1 root root   19 Mar 14 19:23 fingerprint-auth -&gt; fingerprint-auth-ac
-rw-r--r--. 1 root root  702 Mar 14 19:23 fingerprint-auth-ac
-rw-r--r--. 1 root root  590 Mar  5 23:41 gdm-autologin
-rw-r--r--. 1 root root  605 Mar  5 23:41 gdm-fingerprint
-rw-r--r--. 1 root root  341 Mar  5 23:41 gdm-launch-environment
-rw-r--r--. 1 root root  829 Mar  5 23:41 gdm-password
-rw-r--r--. 1 root root  844 Mar  5 23:41 gdm-pin
-rw-r--r--. 1 root root  597 Mar  5 23:41 gdm-smartcard
-rw-r--r--. 1 root root   70 Mar  6 01:41 ksu
-rw-r--r--. 1 root root   97 Mar 26 10:43 liveinst
-rw-r--r--. 1 root root  796 Mar  6 05:58 login
-rw-r--r--. 1 root root  154 Mar  6 04:55 other
-rw-r--r--. 1 root root  188 Jun 10  2014 passwd
lrwxrwxrwx. 1 root root   16 Mar 14 19:23 password-auth -&gt; password-auth-ac
-rw-r--r--. 1 root root  974 Mar 14 19:23 password-auth-ac
-rw-r--r--. 1 root root  510 Mar  6 03:39 pluto
-rw-r--r--. 1 root root  155 Jun  9  2014 polkit-1
lrwxrwxrwx. 1 root root   12 Mar 14 19:23 postlogin -&gt; postlogin-ac
-rw-r--r--. 1 root root  330 Mar 14 19:23 postlogin-ac
-rw-r--r--. 1 root root  144 Jun 10  2014 ppp
-rw-r--r--. 1 root root  681 Mar  6 05:58 remote
-rw-r--r--. 1 root root  143 Mar  6 05:58 runuser
-rw-r--r--. 1 root root  138 Mar  6 05:58 runuser-l
-rw-r--r--. 1 root root  145 Jun  9  2014 setup
lrwxrwxrwx. 1 root root   17 Mar 14 19:23 smartcard-auth -&gt; smartcard-auth-ac
-rw-r--r--. 1 root root  752 Mar 14 19:23 smartcard-auth-ac
lrwxrwxrwx. 1 root root   25 Mar 14 19:20 smtp -&gt; /etc/alternatives/mta-pam
-rw-r--r--. 1 root root   76 Jun 10  2014 smtp.postfix
-rw-r--r--. 1 root root  643 Mar  6 04:44 sshd
-rw-r--r--. 1 root root  540 Mar  6 05:58 su
-rw-r--r--. 1 root root  202 Mar  6 05:42 sudo
-rw-r--r--. 1 root root  187 Mar  6 05:42 sudo-i
-rw-r--r--. 1 root root  137 Mar  6 05:58 su-l
lrwxrwxrwx. 1 root root   14 Mar 14 19:23 system-auth -&gt; system-auth-ac
-rw-r--r--. 1 root root 1015 Mar 14 19:23 system-auth-ac
-rw-r--r--. 1 root root  181 Mar 26 13:03 systemd-user
-rw-r--r--. 1 root root   84 Mar  6 03:11 vlock
-rw-r--r--. 1 root root  297 Mar 12 15:06 vmtoolsd
-rw-r--r--. 1 root root  163 Mar  6 06:11 xserver

</pre>
Most config files here have the same name as the utility that needs to make use of PAM. These commands are referred to as "PAM aware". Hence you can reconfigure one of these files in order to change the ways the utility uses pam to verify the user.

Here you'll find a file called "login", this configures pam's set up when being called by the login binary, if we take a look at this:
<pre>[root@localhost pam.d]# cat login
#%PAM-1.0
auth [user_unknown=ignore success=ok ignore=ignore default=bad] pam_securetty.so
auth       substack     system-auth
auth       include      postlogin
account    required     pam_nologin.so
account    include      system-auth
password   include      system-auth
# pam_selinux.so close should be the first session rule
session    required     pam_selinux.so close
session    required     pam_loginuid.so
session    optional     pam_console.so
# pam_selinux.so open should only be followed by sessions to be executed in the user context
session    required     pam_selinux.so open
session    required     pam_namespace.so
session    optional     pam_keyinit.so force revoke
session    include      system-auth
session    include      postlogin
-session   optional     pam_ck_connector.so
[root@localhost pam.d]#


</pre>
Here the first line (<a href="http://linux-pam.org/Linux-PAM-html/sag-pam_securetty.html">pam_securetty.so</a>) means that the root user can only login from secure terminals as defined by the /etc/securetty file.

Hence this line makes use of a module called pam_securetty.so.

The next line however doesn't call a module, instead it calls another config file, called "system-auth" which also sits in the same directory.

This file is actually is more of generic file that most of the service/tool specific config calls on and the content of this generic file is essentially inserted into the service/tool specific files, which reduces repitition in individual pam config files. If we take a look at this file we find:

You can configure a machine to become an ldap client from the command-line or using a gui tool. We'll explain each approach next.

&nbsp;
<h2>Test your ldap connection</h2>
There are a few checks you can perform to make sure that your ldap

<strong>Need to learn more about:</strong>

<a href="https://access.redhat.com/knowledge/docs/en-US/Red_Hat_Enterprise_Linux/5/html/Deployment_Guide/ch-ldap.html">https://access.redhat.com/knowledge/docs/en-US/Red_Hat_Enterprise_Linux/5/html/Deployment_Guide/ch-ldap.html</a>

http://www.learnitguide.net/2016/01/configure-openldap-server-on-rhel7.htmlhttp://www.zytrax.com/books/ldap/http://www.zytrax.com/books/ldap/ch15/#tls