---
ID: 132
post_title: 'Puppet &#8211; Install/set-up the Puppet Master and it&#8217;s Agents'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-installset-up-the-puppet-master-and-its-agents
published: true
post_date: 2014-08-03 00:00:00
---
This is a walkthrough on how to set up your very own full puppet master-agent environment.

&nbsp;
<h2>
Pre-requisites</h2>
Before your start, you need the following:
<ul>
	<li>At least 2 machines. The first machine will be set up as the puppet master, and the other machine will be set up as a puppet agent. The puppet agent can be either a windows or linux machine. It's recommended that these machines are set up as virtual machines (and we recommend using virtualbox for this) rather than physcal machines. That's because with virtualbox you can quickly clone machines, and take snapshots for easy rollback, if needed.</li>
	<li>Turn master/agent machines into <a title="Linux - NTP Server" href="http://codingbee.net/tutorials/linux/red-hat/linux-ntp-server/">ntp clients</a> so that there internal clocks are synced</li>
	<li><a title="Linux - Give your Linux machine a hostname and domain-name" href="http://codingbee.net/tutorials/linux/red-hat/linux-give-linux-machine-name/"><a href="http://codingbee.net/uncategorized/linux-give-your-linux-machine-a-hostname-and-domain-name/">set hostnames and domains for each machine</a></a>....also recommended to set up ddns if you have a dynamic ip address.</li>
	<li>Both machines are on the same LAN (or have static WAN IPs). You can check this by ensuring that you can ssh from one machine to another using the fqdn, i.e. you can do "ssh {username}@{fqdn}".</li>
</ul>
&nbsp;

For the purpose of this guide, I'm going to use 2 machines which are both running on Centos 6.5 (you can <a href="http://wiki.centos.org/Download">download centos 6.5 here</a>). The first is going to act as my puppet master and it's fqdn is:

<pre>
puppetmaster.codingbee.dyndns.org</pre>

In the remainder of this article, I will be referring to this machine as the puppetmaster.

The second is going be my puppet agent, and it's fqdn is:

puppetagent1.codingbee.dyndns.org

In the remainder of this article, I will be referring to this machine as the puppetagent.

&nbsp;
<h2>Part A - Setting up the Puppet Master</h2>
All the steps in part-A are performed on the puppet master only.
<h3>Step 1 - install "puppetlabs"</h3>
This install software will also deploy the yum repo configs:

For Centos/RHEL 6.5 do:

<pre>rpm -ivh http://yum.puppetlabs.com/puppetlabs-release-el-6.noarch.rpm
</pre>

For Centos/RHEL 7, do:

<pre>rpm -ivh http://yum.puppetlabs.com/puppetlabs-release-el-7.noarch.rpm
</pre>

This command will end up installing puppetlabs package.

<pre>
rpm -qa | grep puppet
</pre>

This packages comes with yum configuration, i.e. a .repo file, so that you can then use the yum command to perform that actual puppet install:

<pre>
[root@localhost yum.repos.d]# pwd
/etc/yum.repos.d
[root@localhost yum.repos.d]# ls -l
total 20
-rw-r--r--. 1 root root 1612 Jul 15 15:18 CentOS-Base.repo
-rw-r--r--. 1 root root  640 Jul 15 15:18 CentOS-Debuginfo.repo
-rw-r--r--. 1 root root 1331 Jul 15 15:18 CentOS-Sources.repo
-rw-r--r--. 1 root root  156 Jul 15 15:18 CentOS-Vault.repo
-rw-r--r--. 1 root root 1250 Jan 22 22:03 puppetlabs.repo    # this is the new file created by the rpm command above
</pre>

<h3>Step 2 - Use yum to finish off installing all puppet components</h3>
After that, to install puppet on a linux server that is going to become the master, do:


<pre>yum install puppet puppet-server facter
</pre>

In the above we installed 3 packages:
<ul>
	<li><strong> puppet-server </strong>- This is the core puppet master component.</li>
	<li><strong> puppet </strong>- This is the puppet-agent component. You don't really need to install this on the puppet master, but in this case I have so that the puppet master server can also act as a puppet agent at the same time. This would mean that the puppet master can be used to make changes to itself</li>
	<li><strong> facter</strong> - This contains the system inventory tool Facter. Facter gathers information, or facts, about your hosts that are used to help customize your Puppet configuration.</li>
</ul>
You can check, if the install has been successful by doing:

<pre>[root@puppetmaster ~]# rpm -qa | grep puppet
puppet-server-3.6.2-1.el6.noarch
puppetlabs-release-6-10.noarch        # this was installed as part of the rpm install done earlier.
puppet-3.6.2-1.el6.noarch
[root@puppetmaster ~]# rpm -qa | grep facter
facter-2.1.0-1.el6.x86_64

</pre>


<h3>Step 3 - Define the "server" parameter in the puppet.conf file</h3>
At this stage, we’re only going to add one entry, server, to the puppet.conf file. The "server" option specifies the (host)name of the Puppet master. We’ll add the server value to the [main]section (if the section doesn’t already exist in your file, then create it). It should like this:
<pre>[root@puppetmaster ~]# cat /etc/puppet/puppet.conf
[main]
    # The Puppet log directory.
    # The default value is '$vardir/log'.
    logdir = /var/log/puppet

    # Where Puppet PID files are kept.
    # The default value is '$vardir/run'.
    rundir = /var/run/puppet

    # Where SSL certificates are kept.
    # The default value is '$confdir/ssl'.
    ssldir = $vardir/ssl

    server=puppetmaster.codingbee.dyndns.org
[agent]
    # The file in which puppetd stores a list of the classes
    # associated with the retrieved configuratiion.  Can be loaded in
    # the separate ``puppet`` executable using the ``--loadclasses``
    # option.
    # The default value is '$confdir/classes.txt'.
    classfile = $vardir/classes.txt

    # Where puppetd caches the local configuration.  An
    # extension indicating the cache format is added automatically.
    # The default value is '$confdir/localconfig'.
    localconfig = $vardir/localconfig
</pre>
In my case the puppetmaster's fqdn is "puppetmaster.codingbee.dyndns.org", as shown above.
<h3>Step 4 - update hosts file (this could be optional)</h3>
This is what I added, but not sure if this is correct or necessary:
<pre>[root@puppetmaster ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
{eth0 ip address} puppetmaster.codingbee.dyndns.org puppetmaster
</pre>
&nbsp;
<h3>step 5 - create the site.pp file</h3>
The following site.pp file needs to exist:

[bash]
[root@puppetmaster manifests]# ls -l /etc/puppet/manifests/site.pp
-rw-r--r--. 1 root root 0 Aug  4 17:53 /etc/puppet/manifests/site.pp
[/bash]

If not then create it along with any folders. At this stage this file can be left as an empty file.
<h3>step 6 - update iptables</h3>
You need to open port 8140 because puppetmaster uses this port to listen in on. There are 2 approaches that could be used to open this:

<strong>Approach 1 - use the iptables command</strong>
Note, this didn't work for me, so I used approach 2 instead. Also I don't this approach will survive a reboot.

In this approach you simply run the following command:

[bash]
$ iptables -A INPUT -p tcp -m state --state NEW --dport 8140 -j ACCEPT [/bash]

In my case it didnt work, it inserted the rule after the "REJECT". I think you can overcome this by <a href="http://serverfault.com/questions/374993/how-do-you-prepend-an-iptables-rather-than-append">prepending the rule by using the I switch, instead of the A switch</a>. i.e. run the following instead:

[bash]
$ iptables -I INPUT -p tcp -m state --state NEW --dport 8140 -j ACCEPT [/bash]

After running this, try:

[bash]
iptables -L
[/bash]

The new rule should be higher than the REJECT statement.

<strong>Approach 2 - update the /etc/sysconfig/iptables file directly</strong>

insert the "8140" line so that it looks like this:
<pre>[root@puppetmaster /]# cat /etc/sysconfig/iptables
# Firewall configuration written by system-config-firewall
# Manual customization of this file is not recommended.
*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
-A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -p udp -m state --state NEW --dport 23 -j ACCEPT
-A INPUT -p tcp -m state --state NEW --dport 23 -j ACCEPT
-A INPUT -p tcp -m state --state NEW --dport 8140 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
COMMIT
</pre>
Then restart iptables (only do this if you followed approach 2):

[bash]
[root@puppetmaster ~]# service iptables restart
iptables: Setting chains to policy ACCEPT: filter                   [   OK   ]
iptables: Flushing firewall rules:                                                 [   OK   ]
iptables: Unloading modules:                                                             [   OK   ]
iptables: Applying firewall rules:                                                 [   OK   ]

[/bash]

<h3>Step 7 - Start the puppet master service (aka puppetmaster)</h3>
Before you start the puppetmaster service, first check if you have any files here:

[bash]
[root@puppetmaster ~]# ls -l /var/lib/puppet/ssl/
ls: cannot access /var/lib/puppet/ssl/: No such file or directory
[root@puppetmaster ~]#
 [/bash]

Notice that even the ssl folder doesn't exist yet.

&nbsp;

Now run:

[bash]
[root@puppetmaster ~]# service puppetmaster status
puppet is stopped
[root@puppetmaster ~]# service puppetmaster start
Starting puppetmaster:                                                                         [   OK   ]
[root@puppetmaster ~]# service puppetmaster status
puppet (pid   2032) is running...
[root@puppetmaster ~]#
 [/bash]

This will have generated the files in the ssl folder now:

&nbsp;

[bash]
[root@puppetmaster ~]# ls -l /var/lib/puppet/ssl/
total 28
drwxr-xr-x. 5 puppet puppet 4096 Aug  4 18:38 ca
drwxr-xr-x. 2 puppet puppet 4096 Aug  4 18:38 certificate_requests
drwxr-xr-x. 2 puppet puppet 4096 Aug  4 18:38 certs
-rw-r--r--. 1 puppet puppet  987 Aug  4 18:38 crl.pem
drwxr-x---. 2 puppet puppet 4096 Aug  4 18:38 private
drwxr-x---. 2 puppet puppet 4096 Aug  4 18:38 private_keys
drwxr-xr-x. 2 puppet puppet 4096 Aug  4 18:38 public_keys
 [/bash]

The presence of these files indicates that the puppetmaster server has started successfully.
<h3>step 8 - ensure puppetmaster starts at boot time</h3>

[bash]
[root@puppetmaster /]# chkconfig | grep puppetmaster
puppetmaster    0:off   1:off   2:off   3:off   4:off   5:off   6:off
[root@puppetmaster /]# chkconfig puppetmaster on
[root@puppetmaster /]# chkconfig | grep puppetmaster
puppetmaster    0:off   1:off   2:on    3:on    4:on    5:on    6:off
[root@puppetmaster /]#
 [/bash]

That's it!
<h2><span style="color: #993300;">Part B - Setting up the Puppet Agent</span></h2>
<h3>Step 1 - install "puppetlabs"</h3>
This install software will also deploy the yum repo configs:

&nbsp;

[bash]
# note it is different for rhel 6 and rhel 7.

rpm -ivh http://yum.puppetlabs.com/puppetlabs-release-el-7.noarch.rpm   # for centos/rhel 7
rpm -ivh http://yum.puppetlabs.com/puppetlabs-release-el-6.noarch.rpm   # for centos/rhel 6
[/bash]

<h3>Step 2 - Use yum to finish off installing all yum components</h3>
After that, to install puppet on a linux server that is going to become the master, do:

[bash]

yum install puppet facter

[/bash]

<h3>Step 3 - updated /etc/hosts</h3>
Not sure if this is required, but I added the follwoign line:
<pre>192.168.1.74 puppetmaster.codingbee.dyndns.org puppetmaster
</pre>
<h4>Step 4 - Update IP tables</h4>
insert the "8139" line so that it looks like this:
<pre>[root@puppetmaster /]# cat /etc/sysconfig/iptables
# Firewall configuration written by system-config-firewall
# Manual customization of this file is not recommended.
*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
-A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -p udp -m state --state NEW --dport 23 -j ACCEPT
-A INPUT -p tcp -m state --state NEW --dport 23 -j ACCEPT
-A INPUT -p tcp -m state --state NEW --dport 8139 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
COMMIT
</pre>
Then restart iptables (only do this if you followed approach 2):

[bash]
[root@puppetmaster ~]# service iptables restart
iptables: Setting chains to policy ACCEPT: filter                   [   OK   ]
iptables: Flushing firewall rules:                                                 [   OK   ]
iptables: Unloading modules:                                                             [   OK   ]
iptables: Applying firewall rules:                                                 [   OK   ]

[/bash]

That's it!
<h2><span style="color: #993300;">Part C - Authenticate the puppet agent with it's puppet master</span></h2>
In Part C we are going alternate running commands on both the puppet-master and the puppet-agent.

<strong>Step 1 - Submit new digital certificate to puppet master (task performed on the puppet-agent)</strong>
Log into the puppet agent and run the following:

[bash]
[root@puppetagent1 ~]# puppet agent --test --server=puppetmaster.codingbee.dyndns.org    # only run this command once.
Info: Caching certificate for ca
Info: csr_attributes file loading from /etc/puppet/csr_attributes.yaml
Info: Creating a new SSL certificate request for puppetagent1.codingbee.dyndns.org
Info: Certificate Request fingerprint (SHA256): 6D:08:DE:7F:43:D0:91:2C:10:44:F3:DE:0D:AF:C5:28:85:E7:8B:9D:9A:A8:6E:26:96:4F:53:CD:26:BA:E6:57
Info: Caching certificate for ca
Exiting; no certificate found and waitforcert is disabled   # note this error message is expected.
[/bash]

(see page 22 of puppet pro book)

The "Exiting; no certificate found and waitforcert is disabled" message above means that the puppet agent has sent a digital certificate to the puppetmaster to sign.

In this case this certificate has the id (aka fingerprint):
<pre>6D:08:DE:7F:43:D0:91:2C:10:44:F3:DE:0D:AF:C5:28:85:E7:8B:9D:9A:A8:6E:26:96:4F:53:CD:26:BA:E6:57</pre>
&nbsp;

<strong>step 2 - check if puppetmaster has recieved the certificate (task performed on the puppet-master)</strong>

Now run the following on   the puppet master to check if it has receieved the certificate:

&nbsp;

&nbsp;

[bash]
puppet# puppet cert list
 &quot;node1.pro-puppet.com&quot; (SHA256) 6F:0D:41:14:BD:2D:FC:CE:1C:DC:11:1E:26:07:4C:08:D0:C7:E8:62:A5:33:E3
 :4B:8B:C6:28:C5:C8:88:1C:C8

  
[/bash]

&nbsp;

<strong>step 3 - sign the certificate and return it to puppet agent.  </strong> <strong>(task performed on the puppet-master)</strong>

now sign this certificate like this:

&nbsp;

&nbsp;

[bash]
[root@puppetmaster examples]# puppet cert sign puppetagent2.codingbee.dyndns.org
Notice: Signed certificate request for puppetagent2.codingbee.dyndns.org
Notice: Removing file Puppet::SSL::CertificateRequest puppetagent2.codingbee.dyndns.org at '/var/lib/puppet/ssl/ca/requests/puppetagent2.codingbee.dyndns.org.pem'
[root@puppetmaster examples]#
[/bash]

&nbsp;

Alternatively, instead of waiting, you can restart the....not sure.
<strong>Step 4 - See if the agent can finally communicate with the master (task performed on the puppet agent)</strong>

[bash]
[root@puppetagent1 ~]# puppet agent --test --server=puppetmaster.codingbee.dyndns.org    # only run this command once.
Info: Retrieving plugin
Info: Caching catalog for node1.pro-puppet.com
Info: Applying configuration version '1365655737
Notice: Finished catalog run in 0.13 seconds
[/bash]

If you get the message like above then it means everything has been done successfully!!!

&nbsp;

If you get a "Error 400 on SERVER" message then don't panic, see pro puppet book, ch1, page 24

&nbsp;

Puppetlabs also provide <a href="https://docs.puppetlabs.com/guides/install_puppet/pre_install.html">puppet open source install instructions</a>.



&nbsp;

Useful info:

Puppet makes use of the following ports:
<table class="wikitable sortable jquery-tablesorter">
<tbody>
<tr style="background: lightsteelblue;">
<td>8139</td>
<td>TCP</td>
<td></td>
<td><a title="Puppet (software)" href="http://en.wikipedia.org/wiki/Puppet_%28software%29">Puppet (software)</a> Client agent</td>
<td>Unofficial</td>
</tr>
<tr style="background: lightsteelblue;">
<td>8140</td>
<td>TCP</td>
<td></td>
<td><a title="Puppet (software)" href="http://en.wikipedia.org/wiki/Puppet_%28software%29">Puppet (software)</a> Master server</td>
<td>Unofficial</td>
</tr>
</tbody>
</table>
That is why these ports were opened as part of the install/setup process above.

&nbsp;

http://xmodulo.com/2014/08/install-puppet-server-client-centos-rhel.html

&nbsp;
https://docs.puppetlabs.com/pe/latest/agent_cert_regen.html
&nbsp;
https://docs.puppetlabs.com/learning/agentprep.html
https://docs.puppetlabs.com/learning/agent_master_basic.html
&nbsp;
https://docs.puppetlabs.com/puppet/#installing-and-configuring

https://docs.puppetlabs.com/guides/install_puppet/pre_install.html (guide for installing open source puppet.)