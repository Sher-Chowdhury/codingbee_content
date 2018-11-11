---
ID: 329
post_title: >
  How to create Kickstart files on
  CentOS/RHEL 7
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/cookbooks/centos/rhcsa-how-to-create-kickstart-files
published: true
post_date: 2015-03-22 00:00:00
---
Once you understand the contents of a kickstart file, it then becomes quite easy to customize it. The kickstart file is arranged into 4 sections. 


<ul>
	<li>Configuration commands</li>
	<li>pre section for writing preinstall scripts</li>
	<li>packages section</li>
	<li>post section</li>
</ul>

These sections neeeds to be declared in the same order as listed above. 


A lot of the stuff in the kickstart file is self explanatory, such as:

<pre>keyboard us              # This sets to keyboard to United States layout</pre>


The <strong>packages</strong> section. The packages section lists all the software that are to be installed during the OS install process:

<ul>
	<li>Lines beginning with an "@" symbol, Indicates a package group that is installed.</li>
	<li>Lines with no symbol represents individual packages that are installed.</li>
	<li>Lines beginning with an "-" symbol, indicates packages that will be excluded from the install.</li>
</ul>



By default a Kickstart file will only create the root user. However you can create other users during the install prcocess by inserting the following entry:


<pre>
user --name=vagrant --plaintext --password vagrant --groups=vagrant,wheel
</pre>

In this example we created a user called "vagrant". 

Note, For security purpose, you should always &lt;l=>encrypt the password before inserting into the kickstart file.

You might find the partition section is commented out, that's because machines can have different hardware in terms of number/capacity of hdds. Therefore as a precaution, this bit is commented out so that you do this manually. But if all your machines have the same HDD setups (and you want all of them to have the same partition/LV setup) then you can uncomment this bit as well. Once you are happy with your kickstart file, you can then start to use kickstart to automate Centos installations.





<h2>Creating a kickstart file</h2>

A Kickstart file automatically gets created by the “anaconda installer” at the end of a RHEL installation. It is saved at the root user’s home directory, and has the name <code>/root/anaconda-ks.cfg</code>





There are main 3 ways to create a kickstart file:

<h3>Approach 1 - Carry out a normal Centos/RHEL installation</h3>
The chance are that you have unknowing already created a ks file the last time you install Centos. It was automatically generated using the answers you selected at the time of the install. You should find the ks file under the root directory, and it has the name "anaconda-ks.cfg". You can use this file as a template to create your own kickstart files.

<h3>Approach 2 - Write it from scratch</h3>
The kickstart file is a simple text file and a lot of it is quite intuitive. There is a long list of kickstart options that you can set. Some of the settings are manadatory. The easiest way to create a kickstart file is by using an existing kickstart file as a template.  



<h3>Approach 3 - Use the GUI tool</h3>
There is a very handy little tool called system-config-kickstart which lets you create your own custom ks files, using a simple GUI. First you need to install the tool:

<pre>
$ yum install system-config-kickstart
</pre>

After that, you can open the tool via the command line:

<pre>
$ system-config-kickstart
</pre>

or from the desktop go to:

<em>Applications => system tools => Kickstart</em>


This will result in the following gui popping up:

<a href="http://codingbee.net/wp-content/uploads/2015/06/hMAroOQ.png"><img src="https://codingbee.net/wp-content/uploads/2015/06/hMAroOQ.png" alt="" width="725" height="598" class="alignnone size-full wp-image-4516" /></a>

Note: this gui tool hasn't been updated in a long time, and hence some powerful features unavailable, e.g. the ability to specify logical volumes, instead this tool is only limited to specifying partitions.






<h2>Validating your kickstart file</h2>

If you manually edit the ks file, be sure to use the <code>ksvalidator</code> command to check that your kickstart file is valid.


<pre>$ vksvalidator /path/to/ks-file</pre>

This tool will find error likes:

<pre>keyxaboard us       # Notice "keyboard" has been mispelt.</pre>

However it won't identify errors relating to:
- URL paths
- package names or groups
- %post or %pre

ksvalidator is part of the <code>system-config-kickstart</code> package. So you need to install this package in order to use ksvalidator.


<h2>Kickstart User Guide</h2>
You can find help info for kickstart by running the following:


<pre>
$ rpm -qd pykickstart
/usr/share/doc/pykickstart-1.99.43.17/COPYING
/usr/share/doc/pykickstart-1.99.43.17/ChangeLog
/usr/share/doc/pykickstart-1.99.43.17/README
<strong>/usr/share/doc/pykickstart-1.99.43.17/kickstart-docs.txt   # Open this in vim.</strong>
/usr/share/doc/pykickstart-1.99.43.17/programmers-guide
/usr/share/man/man1/ksflatten.1.gz
/usr/share/man/man1/ksshell.1.gz
/usr/share/man/man1/ksvalidator.1.gz
/usr/share/man/man1/ksverdiff.1.gz

 
</pre>

Note: pykickstart is a software package that writes/reads kickstart files. 

<h2>Further reading</h2>

<a href="https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html">Redhat Kickstart documentations</a>


<a href="https://access.redhat.com/knowledge/docs/en-US/Red_Hat_Enterprise_Linux/6/html/Installation_Guide/ch-redhat-config-kickstart.html">RedHat’s Kickstart GUI tool guide</a>


<a href="https://academy.redhat.com/instructor/guide/instructorguide_ks.html">An example kickstart file</a>