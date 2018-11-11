---
ID: 229
post_title: 'Vagrant &#8211; Installing Vagrant on Windows'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/vagrant/vagrant-installing-vagrant-on-windows
published: true
post_date: 2014-12-20 00:00:00
---
<h2>Setting up Vagrant on Windows</h2>
You can install virtualbox and vagrant from the powershell command-line with the help of Powershell's community repository, <a href="https://chocolatey.org/">Chocolatey</a>:

1. Open up a powershell terminal (in admin mode)
2. Run the following to connect to the chocolatey library (you can skip this step if the "choco" command already works):
<pre>iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))</pre>
3. Now install vagrant, virtualbox, and cyg-get using the following command:
<pre>cinst virtualbox vagrant cyg-get
</pre>
note: cinst is a an alias for "choco install"

Note: cyg-get is something we are installing as part of an intermediary step for installing openssh. We'll cover more about this later.

4. Also install <a href="https://msysgit.github.io/">git-bash</a>.

5. Check that similar folders to these have now been created:
<ul>
	<li>- VirtualBox : C:\Program Files\Oracle\VirtualBox</li>
	<li>- Vagrant : C:\HashiCorp\Vagrant\bin</li>
	<li>- cyg-get : C:\tools\cygwin and also: C:\tools\cygwin\bin</li>
	<li>- chocolatey : C:\ProgramData\chocolatey\bin</li>
</ul>
Notice that vagrant doesn't get installed in your "program files" folder like other software do. The same is true for cyg-get.

10. Once you have located all these directories, you need to append all of them to the window's path environment variable.

11. Close your powershell terminal and reopen it again in admin mode. This is so that your powershell loads in the newly edited path variable.

12. Verify that vagrant is installed correctly by running the following:
<pre>PS C:\Windows\system32&gt; vagrant -v
Vagrant 1.6.5
</pre>
12. Verify that virtualbox is installed properly by running the following:
<pre>PS C:\Windows\system32&gt; vboxmanage -v
4.3.12r93733
</pre>
12c. Install the following components, but this time using the cyg-get command:
<pre>cyg-get openssh 
cyg-get rsync 
cyg-get ncurses
</pre>



You are now ready to provision your first VM using vagrant. 




&nbsp;