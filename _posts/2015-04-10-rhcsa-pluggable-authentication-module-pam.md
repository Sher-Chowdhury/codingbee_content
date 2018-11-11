---
ID: 353
post_title: 'RHCSA &#8211; Pluggable Authentication Module (PAM)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-pluggable-authentication-module-pam
published: true
post_date: 2015-04-10 00:00:00
---
The following guide is really useful:


https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/PAM_Configuration_Files.html#PAM_Configuration_File_Format

This guide explains things better than anything else that I can find. 

There are a number of commandline utilities that needs to authenticate the user before it can provide service to it. For example we have the "login" utility:




Once user has successfully authenticated against the login utility, the login utility will then give the user access to the bash terminal. 

The login utility behind the scenes makes use of PAM to do the actual authentication. 

PAM let's you configure how authentication happens on a tool by tool basis. Each tool have respective pam config file, where you can do the configuration. All these config files can be found here:

<pre>
$ ls -l /etc/pam.d/
total 156
-rw-r--r--. 1 root root  272 Oct  7  2014 atd
-rw-r--r--. 1 root root  192 Mar  6 05:58 chfn
-rw-r--r--. 1 root root  192 Mar  6 05:58 chsh
-rw-r--r--. 1 root root  232 Mar  6 04:55 config-util
-rw-r--r--. 1 root root  293 Jul 30  2014 crond
-r--r--r--. 1 root root  146 Mar  5 22:51 cups
lrwxrwxrwx. 1 root root   19 Mar 14 19:23 fingerprint-auth -> fingerprint-auth-ac
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
lrwxrwxrwx. 1 root root   16 Mar 14 19:23 password-auth -> password-auth-ac
-rw-r--r--. 1 root root  974 Mar 14 19:23 password-auth-ac
-rw-r--r--. 1 root root  510 Mar  6 03:39 pluto
-rw-r--r--. 1 root root  155 Jun  9  2014 polkit-1
lrwxrwxrwx. 1 root root   12 Mar 14 19:23 postlogin -> postlogin-ac
-rw-r--r--. 1 root root  330 Mar 14 19:23 postlogin-ac
-rw-r--r--. 1 root root  144 Jun 10  2014 ppp
-rw-r--r--. 1 root root  681 Mar  6 05:58 remote
-rw-r--r--. 1 root root  143 Mar  6 05:58 runuser
-rw-r--r--. 1 root root  138 Mar  6 05:58 runuser-l
-rw-r--r--. 1 root root  145 Jun  9  2014 setup
lrwxrwxrwx. 1 root root   17 Mar 14 19:23 smartcard-auth -> smartcard-auth-ac
-rw-r--r--. 1 root root  752 Mar 14 19:23 smartcard-auth-ac
lrwxrwxrwx. 1 root root   25 Mar 14 19:20 smtp -> /etc/alternatives/mta-pam
-rw-r--r--. 1 root root   76 Jun 10  2014 smtp.postfix
-rw-r--r--. 1 root root  643 Mar  6 04:44 sshd
-rw-r--r--. 1 root root  540 Mar  6 05:58 su
-rw-r--r--. 1 root root  202 Mar  6 05:42 sudo
-rw-r--r--. 1 root root  187 Mar  6 05:42 sudo-i
-rw-r--r--. 1 root root  137 Mar  6 05:58 su-l
lrwxrwxrwx. 1 root root   14 Mar 14 19:23 system-auth -> system-auth-ac
-rw-r--r--. 1 root root 1015 Mar 14 19:23 system-auth-ac
-rw-r--r--. 1 root root  181 Mar 26 13:03 systemd-user
-rw-r--r--. 1 root root   84 Mar  6 03:11 vlock
-rw-r--r--. 1 root root  297 Mar 12 15:06 vmtoolsd
-rw-r--r--. 1 root root  163 Mar  6 06:11 xserver

</pre>

Most config files here have the same name as the utility that needs to make use of PAM. These commands are referred to as "PAM aware". Hence you can reconfigure one of these files in order to change the ways the utility uses pam to verify the user. 

Here you'll find a file called "login", this configures pam's set up when being called by the login binary, if we take a look at this:


<pre>

[root@localhost pam.d]# cat login
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


Notice that each line is made up of <a href="https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/PAM_Configuration_Files.html#PAM_Configuration_File_Format">4 fields</a>, These fields have the following labels:

 
<pre>
module_interface    control_flag    module_name    module_arguments (optional)
</pre>


Note: service isn't used that much. instead the config file is named after the service instead. 


<h3>The module_interface field</h3>

The first field in the configuration file is the module-type indicating which of four general PAM management services that the module will provide to the application. This field is referred to the "type" field

PAM make 4 general pam services available to each application specific pam module:


<ul>
	<li><strong>auth</strong> - Determines whether the user is who they claim to be, usually by a password, or other means, such as biometrics.</li>
	<li><strong>account</strong> - Determines whether the user is allowed to access the service. This is more about user right's rather than authenticating user. </li>
	<li><strong>password</strong> - Provides a mechanism for user to change their password, this is typically using their current password</li>
	<li><strong>session</strong> - Things that are done before and/or after the user has been authenticated. This might included things such as mounting/unmounting the user home directory, logging their login/logout, and restricting/unrestricting the services available to the user.</li>
</ul>

Note,

<h3>The control_flag field</h3>

The second field is called the "<a href="http://www.linux-pam.org/Linux-PAM-html/sag-configuration-file.html">control</a>" field, it can take the following values:

<ul>
	<li><strong>required</strong> - The module result must be successful for authentication to continue. If the test fails at this point, the user is not notified until the results of all module tests that reference that interface are complete.</li>
	<li><strong>requisite</strong> -</li>
	<li><strong>sufficient</strong> - The module result is ignored if it fails. However, if the result of a module flagged sufficient is successful and no previous modules flagged required have failed, then no other results are required and the user is authenticated to the service.</li>
	<li><strong>optional</strong> -</li>
	<li><strong>include</strong> -</li>
	<li><strong>substack</strong> -</li>
	<li><strong>{complex}</strong> -</li>
</ul>







The type token tells PAM what type of authentication is to be used for this module (which in this example is the login module). Modules of the same type can be "stacked", requiring a user to meet multiple requirements to be authenticated. 


<h3>The modulepath field</h3>

You can find info for all the pam modules here:

<pre>
$ ls /usr/share/doc/pam-1.1.8/txts
README.pam_access     README.pam_ftp        README.pam_namespace   README.pam_succeed_if
README.pam_chroot     README.pam_group      README.pam_nologin     README.pam_tally
README.pam_console    README.pam_issue      README.pam_permit      README.pam_tally2
README.pam_cracklib   README.pam_keyinit    README.pam_postgresok  README.pam_time
README.pam_debug      README.pam_lastlog    README.pam_pwhistory   README.pam_timestamp
README.pam_deny       README.pam_limits     README.pam_rhosts      README.pam_tty_audit
README.pam_echo       README.pam_listfile   README.pam_rootok      README.pam_umask
README.pam_env        README.pam_localuser  README.pam_securetty   README.pam_unix
README.pam_exec       README.pam_loginuid   README.pam_selinux     README.pam_userdb
README.pam_faildelay  README.pam_mail       README.pam_sepermit    README.pam_warn
README.pam_faillock   README.pam_mkhomedir  README.pam_shells      README.pam_wheel
README.pam_filter     README.pam_motd       README.pam_stress      README.pam_xauth
</pre>

As you can see each module has it's own readme file. 

<pre>
less /usr/share/doc/pam-1.1.8/Linux-PAM_SAG.txt
</pre>

This <a href="http://linux-pam.org/Linux-PAM-html/Linux-PAM_SAG.html">pam guide</a> is also available online.

This guide covers all the various pam modules that are available. 








<a href="http://www.linux-pam.org/Linux-PAM-html/Linux-PAM_SAG.html">The Linux-PAM System Administrators Guide</a>

http://linux.die.net/man/5/system-auth-ac
http://linux.die.net/man/8/authconfig

http://www.linuxgeek.net/documentation/authentication.phtml

https://www.google.co.uk/search?q=authconfig+enablewinbindauth+&ie=utf-8&oe=utf-8&aq=t&rls=org.mozilla:en-GB:official&client=firefox-a&channel=sb&gfe_rd=cr&ei=0XHwVKC5C4OP-wap0oCADA#safe=off&rls=org.mozilla:en-GB:official&channel=sb&q=edit+%2Fetc%2Fpam.d%2Fsystem-auth






<h3>See also</h3>
https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/Pluggable_Authentication_Modules.html