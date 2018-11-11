---
ID: 464
post_title: Yum Repository GPG keys
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-yum-repository-gpg-keys
published: true
post_date: 2015-10-06 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to create a repo using the baseurl as 'https://dl.fedoraproject.org/pub/epel/7/x86_64/'?"]<span style='font-size:15px'>$ yum-config-manager <span style='letter-spacing:0.1px'>-</span>-add-repo=https://dl.fedoraproject.org/pub/epel/7/x86_64/</span>[/toggle]
[toggle title="What is the command?"]
/etc/pki/rpm-gpg/
[/toggle]
[toggle title="What is the command?"]


[/toggle]
[toggle title="What is the command?"]


[/toggle]
[toggle title="What is the command?"]


[/toggle]
[toggle title="What is the command?"]


[/toggle]
[toggle title="What is the command?"]


[/toggle]
[toggle title="What is the command?"]


[/toggle]
[toggle title="What is the command?"]


[/toggle]
[toggle title="What is the command?"]


[/toggle]
[/accordion]

<hr/>




Yum makes use of GPG keys as a way to ensure that our machine downloads rpm packages from an authenticated source. 

To set up GPG keys for a yum repo, let's first get a url for an yum repo, in our example, we'll use the <a href="https://fedoraproject.org/wiki/EPEL">epel yum repo</a>:

<pre>
https://dl.fedoraproject.org/pub/epel/7/x86_64/
</pre>

Let's first create a .repo file for this yum repo using the yum-config-manager:


<pre>
$ <strong>yum-config-manager --add-repo=https://dl.fedoraproject.org/pub/epel/7/x86_64/</strong>
Loaded plugins: fastestmirror, langpacks
adding repo from: https://dl.fedoraproject.org/pub/epel/7/x86_64/

[dl.fedoraproject.org_pub_epel_7_x86_64_]
name=added from: https://dl.fedoraproject.org/pub/epel/7/x86_64/
baseurl=https://dl.fedoraproject.org/pub/epel/7/x86_64/
enabled=1
</pre>

This command generated the following .repo file:

<pre>
$ cat /etc/yum.repos.d/dl.fedoraproject.org_pub_epel_7_x86_64_.repo

[dl.fedoraproject.org_pub_epel_7_x86_64_]
name=added from: https://dl.fedoraproject.org/pub/epel/7/x86_64/
baseurl=https://dl.fedoraproject.org/pub/epel/7/x86_64/
enabled=1
</pre>

By default, yum-config-manager creates a repo that isn't GPG secured. Although at this point our epel repo is in a usable state. To enable GPG for our new repo, We'll need to append a couple more lines to our new .repo file. 

To add these line, we first need to locate the epel repo's GPG key. In this case the gpg key is located further up epel's directory tree:


<pre>
https://dl.fedoraproject.org/pub/epel/
</pre>

It is best practice to download all gpg keys to the <code>/etc/pki/rpm-gpg/</code> directory:

<pre>
$ ls -l /etc/pki/rpm-gpg/
total 32
-rw-r--r--. 1 root root 1690 Mar 31  2015 RPM-GPG-KEY-CentOS-7
-rw-r--r--. 1 root root 1004 Mar 31  2015 RPM-GPG-KEY-CentOS-Debug-7
-rw-r--r--. 1 root root 1690 Mar 31  2015 RPM-GPG-KEY-CentOS-Testing-7
-rw-r--r--. 1 root root 1662 Nov 25  2014 RPM-GPG-KEY-EPEL-7
-rw-r--r--. 1 root root 3140 Sep 11 10:35 RPM-GPG-KEY-foreman
-rw-r--r--. 1 root root 5567 Aug 22  2014 RPM-GPG-KEY-nightly-puppetlabs
-rw-r--r--. 1 root root 1716 Aug 22  2014 RPM-GPG-KEY-puppetlabs
</pre>

Therefore let's download the epel GPG key into this directory using <strong>wget</strong>:


<pre>
$ cd /etc/pki/rpm-gpg/
$ <strong>wget https://dl.fedoraproject.org/pub/epel/RPM-GPG-KEY-EPEL-7</strong>
--2015-10-06 20:28:49--  https://dl.fedoraproject.org/pub/epel/RPM-GPG-KEY-EPEL-7
Resolving dl.fedoraproject.org (dl.fedoraproject.org)... 209.132.181.24, 209.132.181.23, 209.132.181.26, ...
Connecting to dl.fedoraproject.org (dl.fedoraproject.org)|209.132.181.24|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1662 (1.6K)
Saving to: ‘RPM-GPG-KEY-EPEL-7’

100%[=============================================================>] 1,662       --.-K/s   in 0s

2015-10-06 20:28:50 (18.0 MB/s) - ‘RPM-GPG-KEY-EPEL-7’ saved [1662/1662]

</pre>

Now we have to append the following lines to our new .repo file:

<pre>
$ cat /etc/yum.repos.d/dl.fedoraproject.org_pub_epel_7_x86_64_.repo

[dl.fedoraproject.org_pub_epel_7_x86_64_]
name=added from: https://dl.fedoraproject.org/pub/epel/7/x86_64/
baseurl=https://dl.fedoraproject.org/pub/epel/7/x86_64/
enabled=1
<mark>gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7</mark>
</pre>

That's all you need to do.


Tip, some popular repos, such as the epel repo can be installed in the form of an rpm package themselves. So instead of doing all the above, you can simply do: 


<pre>
$ yum install epel-release
</pre>

This will effectively drop in the gpg key and the .repo file in the relevant directories.