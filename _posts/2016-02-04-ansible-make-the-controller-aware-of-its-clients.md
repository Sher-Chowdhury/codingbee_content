---
ID: 530
post_title: 'Ansible &#8211; Make the controller aware of it&#8217;s clients'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ansible/ansible-make-the-controller-aware-of-its-clients
published: true
post_date: 2016-02-04 00:00:00
---
Before the controller can manage the clients, it first need to provide ansible an  inventory of the clients it is allowed to manage. This inventory needs to be in the form of a file called "hosts".

You can choose where you store the hosts file. But the default location ansible will look for the hosts file is specified in ansible's main config file:

&nbsp;
<pre>[root@controller ~]# cat /etc/ansible/ansible.cfg | grep "^inventory"
inventory = /etc/ansible/hosts</pre>
In my case, my file looks like:

&nbsp;
<pre>
$ cat /etc/ansible/hosts
# Ungrouped hosts, specify before any group headers.
ansibleclient01.local
ansibleclient02.local

# A collection of hosts belonging to the 'clients' group
[clients]
ansibleclient01.local
ansibleclient02.local

</pre>

&nbsp;

&nbsp;

&nbsp;

After that you can check if your controller  can ping it's clients, you can run the following command:

&nbsp;
<pre>$ ansible ansibleclient01.local -m ping
ansibleclient01.local | SUCCESS =&gt; {
 "changed": false,
 "ping": "pong"
}</pre>
&nbsp;

Or you can ping a group of clients:

&nbsp;
<pre>$ ansible clients --module-name ping
ansibleclient01.local | SUCCESS =&gt; {
 "changed": false,
 "ping": "pong"
}
ansibleclient02.local | SUCCESS =&gt; {
 "changed": false,
 "ping": "pong"
}</pre>
Note: here the "change" is showing as false, this means the ping module hasn't change the client's state in anyway.

&nbsp;

You can also run custom commands using the "command" module:

&nbsp;
<pre>$ ansible clients --module-name command --args "cat /etc/hostname"
ansibleclient01.local | SUCCESS | rc=0 &gt;&gt;
ansibleclient01.local

ansibleclient02.local | SUCCESS | rc=0 &gt;&gt;
ansibleclient02.local</pre>
&nbsp;

Notice that the "command" module requires arguements.

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;