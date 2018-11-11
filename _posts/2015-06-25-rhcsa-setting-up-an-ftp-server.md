---
ID: 434
post_title: 'RHCSA &#8211; Setting up an FTP server'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-setting-up-an-ftp-server
published: true
post_date: 2015-06-25 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to install the ftp server software?"]
$ yum install vsftpd
[/toggle]
[toggle title="What is the location of this packages main config file?"]
/etc/vsftpd/vsftpd.conf   
[/toggle]
[toggle title="What is the command to display help info about this config file?"]
$ man 5 vsftpd.conf
[/toggle]
[toggle title="What directory do you install the files in?"]
/var/ftp
[/toggle]
[toggle title="What is the command to check the ftp service?"]
$ systemctl status vsftpd
[/toggle]
[toggle title="What is the command to persistantly add the ftp service to the firewall?"]
$ firewall-cmd --permanent --add-service=ftp
$ systemctl restart firewalld
[/toggle]
[toggle title="What is the command to check that ftp service is listening on the ftp port?"]
$ ss -at | grep ftp
LISTEN     0      32        :::ftp         :::*
[/toggle]
[toggle title="What is the command to fix all file SELinux context issues?"]
$ restorecon -R /var/ftp
[/toggle]
[toggle title="What is the command to persistantly+verbosely enable the ftp_home_dir boolean?"]
$ setsebool -PV ftp_home_dir on
[/toggle]
[toggle title="What is the command to install the ftp client?"]
$ yum install lftp
[/toggle]
[toggle title="What is the command to start a new ftp interactive session remotely?"]
$ lftp localhost
[/toggle]
[/accordion]

<hr/>



First off, you might think configuring an ftp server will be similar to how we do it for httpd. For example, there is no equivalent "documentroot" setting defined in the main config file, but more about this later.  


<h2>Install vsftpd</h2>
The main package you need to install for setting up an ftp server for your machine is called "vsftpd":

<pre>
$ yum install vsftpd
</pre>

<h2>Configure vsfptd</h2>
Once that's done you'll find the main config file is:

<pre>
/etc/vsftpd/vsftpd.conf   
</pre>

This file is quite self explanatory, the default settings should be enough to start sharing folders right away, but you can find more help info here if you want to do any customisations:

<pre>
$ man 5 vsftpd.conf
</pre>


<h2>Add files into the ftp share directory</h2>
The next thing you need to do is place files in vsftp's "share folder". The only way to identify this is by looking at the ftp's service account's directory:

<pre>
$ cat /etc/passwd | grep ftp
ftp:x:14:50:FTP User:<mark>/var/ftp</mark>:/sbin/nologin
</pre>

Surprising this in the only way to locate this "share folder". In this folder you'll find the "pub":

<pre>
$ pwd
/var/ftp
$ ls -l
total 0
drwxr-xr-x. 2 root root 6 Jun 10  2014 pub
</pre>

Therefore this is folder where you drop files to make it available via ftp:

<pre>
/var/ftp/pub
</pre>

<h2>Start the vsftpd service</h2>
Now, the ftp service is:

<pre>
$ systemctl status vsftpd
vsftpd.service - Vsftpd ftp daemon
   Loaded: loaded (/usr/lib/systemd/system/vsftpd.service; disabled)
   Active: inactive (dead)
</pre>

So to start sharing we need to enable+start this service:

<pre>
$ systemctl enable vsftpd
ln -s '/usr/lib/systemd/system/vsftpd.service' '/etc/systemd/system/multi-user.target.wants/vsftpd.service'
$ systemctl start vsftpd
</pre>




<h2>Add FTP service to relevant firewalld zone</h2>

Now we need to tell the firewall to allow vsftpd to listen on the ftp port (port 21):

<pre>
$ firewall-cmd --list-all
public (default, active)
  interfaces: enp0s3
  sources:
  services: dhcpv6-client ssh
  ports:
  masquerade: no
  forward-ports:
  icmp-blocks:
  rich rules:
</pre>

Now lets add this service to the current firewalld zone:

<pre>$ firewall-cmd --add-service=ftp
success
$ firewall-cmd --permanent --add-service=ftp
success
</pre>



Next you should check that the vsftpd service is listening:

<pre>
ss -at | grep ftp
LISTEN     0      32        :::ftp         :::*
</pre>



<h2>Check that file SELinux contexts are ok</h2> 

Ensure the folders that contains the files/folders to be shared has the "public_content_t" type security context:

<pre>
$ ls -lZ /var/ | grep ftp
drwxr-xr-x. root root system_u:object_r:<strong>public_content_t</strong>:s0 ftp

$ ls -lZ /var/ftp/ | grep pub
drwxr-xr-x. root root system_u:object_r:<strong>public_content_t</strong>:s0 pub
</pre>

If it isn't then run the following to recursively correct them:

<pre>
$ restorecon -R /var/ftp
</pre>


<h2>SELinux Boolean Settings</h2>
SEBooleans is another aspect you SELinux that you might need to configure as part of setting up vsftpd. Also check SEboolean settings:

<pre>
$ getsebool -a | grep ftp
ftp_home_dir --> on
ftpd_anon_write --> off
ftpd_connect_all_unreserved --> off
ftpd_connect_db --> off
ftpd_full_access --> off
ftpd_use_cifs --> off
ftpd_use_fusefs --> off
ftpd_use_nfs --> off
ftpd_use_passive_mode --> off
httpd_can_connect_ftp --> off
httpd_enable_ftp_server --> off
sftpd_anon_write --> off
sftpd_enable_homedirs --> off
sftpd_full_access --> off
sftpd_write_ssh_home --> off
tftp_anon_write --> off
tftp_home_dir --> off
</pre>

Note, you can run the <code>semanage boolean -l | grep ftp</code> to find out what these settings means. 

Where necessary you can change settings, e.g to enable 'ftp_home_dir', we do:


<pre>
$ setsebool -PV ftp_home_dir on
</pre>

Where "P" is for persistant. And "V" is for verbose mode, in case there are any error messages. 











<h2>Test your vsftpd setup</h2>
Now to test whether our ftp service is working, we need to install an ftp client, this ftp client is called:

<pre>
$ yum install lftp
</pre>

After that, simply do:

<pre>
$ lftp localhost
</pre>

This starts an interactive shell. Here's an example of it in action:

<pre>
$ lftp localhost
lftp localhost:~> ls
drwxr-xr-x    2 0        0              25 Nov 10 21:06 pub
lftp localhost:/> cd pub/
lftp localhost:/pub> ls
-rw-r--r--    1 0        0              36 Nov 10 21:06 testfile.txt
lftp localhost:/pub> get testfile.txt
36 bytes transferred
lftp localhost:/pub> quit
$ ls -l
total 4
-rw-rw-r--. 1 tom tom 36 Nov 10 21:06 testfile.txt
</pre>









<h2>Other common configurations</h2>
There are basically 2 types of users who you can provide access to your ftp server:


<ul>
	<li>authenticated users - they do need to provide login credentials</li>
	<li>anonymous users - they don't need to provide login credentials</li>
</ul>


You have all kinds of possibilities:

<ul>
	<li>Restrict access to authenticated users only</li>
	<li>Provide anonymous access - this approach is ideal if you are setting up an ftp server to share files with the public</li>
	<li>tiered access - anonymous users can access some files, whereas authenticated can access all the files that anonymous user can, but they also have access to other restricted files too.</li>
</ul>



Also notice that "others" has r+x permissions. That is so to allow anonymous users to access this directory (enabled with "x") and view/download it's content (enabled with "r")