---
ID: 463
post_title: Create a local Yum repo from an ISO file
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-create-a-local-yum-repo-from-an-iso-file
published: true
post_date: 2015-10-04 00:00:00
---
If you explore the contents of your CentOS/RHEL installation dvd you would find that it contains really big yum repos. You can identify a yum repo by any directory that contains a folder called <strong>repodata</strong>

If you have used an ISO file to install your OS, then that ISO file is simply the file equivalent of the installation DVD (for this article we will ignore DVDs and assume you have an ISO). 

We can therefore configure our ISO/DVD to act as a local yum repo. To do this, you first need to mount the iso.

To mount the iso, we first create a mount point:

<pre>
$ mkdir -p /data/iso
</pre>

Then we mount the iso using the mount command:

<pre>
$ mount -o loop /vagrant/CentOS-6.6-x86_64-minimal.iso /data/iso/
mount: /dev/loop0 is write-protected, mounting read-only
</pre>

Note: we need to use the "loop" option when mounting an ISO or dvd. 


We can then check this has worked using df:

<pre>
$ df -h
Filesystem                            Size  Used Avail Use% Mounted on
/dev/mapper/centos_puppetmaster-root   38G  4.7G   33G  13% /
devtmpfs                              488M     0  488M   0% /dev
tmpfs                                 497M   80K  497M   1% /dev/shm
tmpfs                                 497M  7.0M  490M   2% /run
tmpfs                                 497M     0  497M   0% /sys/fs/cgroup
/dev/sda1                             497M  149M  349M  30% /boot
none                                  224G  156G   68G  70% /vagrant
<strong>/dev/loop0                            383M  383M     0 100% /data/iso</strong>
</pre>

or we can check using the mount command:

<pre>
$ mount | grep '/data/iso'
/vagrant/CentOS-6.6-x86_64-minimal.iso on /data/iso type iso9660 (ro,relatime)
</pre>

Obviously another way to check is by going into the mount point itself, and see what's there:

<pre>
$ ls -l /data/iso/
total 82
-r--r--r--. 1 root root    14 Oct 24  2014 CentOS_BuildTag
dr-xr-xr-x. 3 root root  2048 Oct 24  2014 EFI
-r--r--r--. 1 root root   212 Nov 27  2013 EULA
-r--r--r--. 1 root root 18009 Nov 27  2013 GPL
dr-xr-xr-x. 3 root root  2048 Oct 24  2014 images
dr-xr-xr-x. 2 root root  2048 Oct 24  2014 isolinux
dr-xr-xr-x. 2 root root 40960 Oct 24  2014 Packages
-r--r--r--. 1 root root  1354 Oct 19  2014 RELEASE-NOTES-en-US.html
<strong>dr-xr-xr-x. 2 root root  4096 Oct 24  2014 repodata
</strong>-r--r--r--. 1 root root  1706 Nov 27  2013 RPM-GPG-KEY-CentOS-6
-r--r--r--. 1 root root  1730 Nov 27  2013 RPM-GPG-KEY-CentOS-Debug-6
-r--r--r--. 1 root root  1730 Nov 27  2013 RPM-GPG-KEY-CentOS-Security-6
-r--r--r--. 1 root root  1734 Nov 27  2013 RPM-GPG-KEY-CentOS-Testing-6
-r--r--r--. 1 root root  3380 Oct 24  2014 TRANS.TBL
</pre>

Now to set up the local repo we can manually create the .repo file. Byt it is easier to just use the <strong>yum-config-manager</strong> command:

<pre>
$ yum-config-manager --add-repo=file:///data/iso
</pre>
Note the three forward slashes. 

Now we can check this has worked by using <strong>yum repolist</strong>:

<pre>
$ yum repolist
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.clouvider.net
 * epel: fedora.cu.be
 * extras: mirrors.clouvider.net
 * updates: centos.muzzy.org.uk
repo id                     repo name                              status
base/7/x86_64               CentOS-7 - Base                        8,652
<strong>data_iso                    added from: file:///data/iso             248
</strong>epel/x86_64                 Extra Packages for Enterprise Linux 7  8,545
extras/7/x86_64             CentOS-7 - Extras                        214
foreman/x86_64              Foreman 1.9                              367
foreman-plugins/x86_64      Foreman plugins 1.9                      216
puppetlabs-deps/x86_64      Puppet Labs Dependencies El 7 - x86_64    17
puppetlabs-products/x86_64  Puppet Labs Products El 7 - x86_64       191
rhscl-ruby193-epel-7-x86_64 Ruby193 - epel-7-x86_64                  405
rhscl-v8314-epel-7-x86_64   V8 3.14.5.10 - epel-7-x86_64              21
updates/7/x86_64            CentOS-7 - Updates                     1,501
repolist: 20,377

</pre>



[post-content post_name=rhsca-quiz] 

[accordion]
[toggle title="What are three steps needed for yum to access a yum repo that's stored on a cd?"]
1. Create a mount point
2. mount the iso onto the mount point
3. create a repo file pointing to the new mount point
[/toggle]
[toggle title="What is the command to mount the iso, /tmp/library.iso onto the mount point, /data/iso?"]
$ mount -o loop /tmp/library.iso /data/iso
[/toggle]
[toggle title="What is an indicator that a folder is actually a yum repo?"]
It contains a folder called "repodata"
[/toggle]
[toggle title="What is the command to create a yum repo pointing to this newly mounted iso (assuming the folder, /data/iso/repodata exists)?"]
$ yum-config-manager --add-repo=file:///data/iso
[/toggle]
[/accordion]