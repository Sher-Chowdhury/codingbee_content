---
ID: 492
post_title: 'Kerberos &#8211; A &#8216;Hello World&#8217; example on CentOS/RHEL 7'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/rhce-kerberos
published: true
post_date: 2018-02-25 00:00:00
---
Kerberos is a network authentication protocol that's designed to allow machines to securely authenticate one another over a public network. In a typical Kerberos setup, there is a single Kerberos server and lots of kerberos clients. The Kerberos server is often referred to as the KDC server, where KDC is short for Key Distribution Center. Before you can implement kerberos, there are 2 key requirements that needs to be met:

<ul>
	<li>all boxes must have fully qualified domain names</li>
	<li>time needs to be accurately synced across all boxes</li>
</ul>




The best way to understand how kerberos works is to go through a working example. You can follow this example with this <a href="https://github.com/Sher-Chowdhury/CentOS7-kerberos-demo" rel="nofollow">Kerberos vagrant project</a>. In this vagrant project we have the following scenario:



<pre style="font-size:16px;line-height:100%;">

            +------------------------------------------------+
            |                                                |
            |                Kerberos Server                 |
            |                kdc.codingbee.net               |
            |                (192.168.10.100)                |
            |                                                |
            +-------+--------------------------------+-------+
                    |                                |
                    |                                |
                    |                                |
                    v                                v
+----------------------------+               +----------------------------+
|                            |               |                            |
|  krb client 1              |     ssh       |  krb client 2              |
|  krb-client1.codingbee.net | <-----------> |  krb-client1.codingbee.net |
|  (192.168.10.101)          |               |  (192.168.10.102)          |
|                            |               |                            |
+----------------------------+               +----------------------------+

</pre>

The goal is that we want to ssh from krb-client-1 to krb-client-2 (and vice versa) using Kerberos authentication instead of entering a password, or use ssh keys. So the first thing to do is to create the above setup. With vagrant this is easy, all you do is a 'vagrant up'. But if you were to do this manually then it would mean creating the Kerberos Server, and then the Kerberos Clients and then make sure that they can all ping each other. 

<h2>Kerberos Server Setup</h2> 
In the RHCE exam, you don't need to know how to setup a Kerberos server, but it is still useful to know how to do it. 

We can break down the installation process into 4 stages:

<ol>
	<li>Setup valid fqdns</li>
        <li>Setup Kerberos Server</li>
	<li>Register trusted entities to the Kerberos Database</li>
	<li>Configure firewalld to accept kerberos related traffic</li>
	<li>Locally test our setup - configure ssh</li>
</ol>



<h2><a id='setup-kerberos-server'>Setup the Kerberos Server</a></h2>
To start with, we need to mimic valid fqdns by inserting the following entries into the <code>/etc/hosts</code> file:

<pre>
192.168.10.100 kdc.codingbee.net
192.168.10.101 krb-client1.codingbee.net
192.168.10.102 krb-client2.codingbee.net
</pre>

To start with we need to install the necessary rpms:

<pre>
$ yum install krb5-server krb5-workstation pam_krb5
</pre>

<strong>krb5-server</strong> - this is the main kerberos server side software. 

<strong>krb5-workstation</strong> - this is the keberos client side software. It's optional to install it on the kerberos server, but is useful for Kerberos troubleshooting purposes. 

<strong>pam_krb5</strong> - this is the pam krb5 module. ssh does it's authentication via pam, hence we need to make pam krb5 aware. 


Next we need to update the following file:

<pre>
[root@kdc ~]# cat /var/kerberos/krb5kdc/kdc.conf
[kdcdefaults]
 kdc_ports = 88
 kdc_tcp_ports = 88

[realms]
 <strong>EXAMPLE.COM</strong> = {
  #master_key_type = aes256-cts
  acl_file = /var/kerberos/krb5kdc/kadm5.acl
  dict_file = /usr/share/dict/words
  admin_keytab = /var/kerberos/krb5kdc/kadm5.keytab
  supported_enctypes = aes256-cts:normal aes128-cts:normal des3-hmac-sha1:normal arcfour-hmac:normal camellia256-cts:normal camellia128-cts:normal des-hmac-sha1:normal des-cbc-md5:normal des-cbc-crc:normal
 }

</pre>

Basically we need to specify a realm:

<pre>
[root@kdc ~]# cat /var/kerberos/krb5kdc/kdc.conf
[kdcdefaults]
 kdc_ports = 88
 kdc_tcp_ports = 88

[realms]
 <strong>CODINGBEE.NET = {
  master_key_type = aes256-cts
  default_principle_flags = +preauth</strong>
  acl_file = /var/kerberos/krb5kdc/kadm5.acl
  dict_file = /usr/share/dict/words
  admin_keytab = /var/kerberos/krb5kdc/kadm5.keytab
  supported_enctypes = aes256-cts:normal aes128-cts:normal des3-hmac-sha1:normal arcfour-hmac:normal camellia256-cts:normal camellia128-cts:normal des-hmac-sha1:normal des-cbc-md5:normal des-cbc-crc:normal
 }
</pre>

Note, you have to match the case, i.e. you need to write it in upper case. 

Note, we also uncommented master_key_type and inserted the 'default_principle_flags = +preauth' line. That is optional, and is done so to add a bit more security hardening. But it also breaks backward compatibility to kerberos 4 and older. 

Then we do the same thing to the following small file:

<pre>
[root@kdc ~]# cat /var/kerberos/krb5kdc/kadm5.acl
*/admin@EXAMPLE.COM	*
</pre>

Which means it ends up looking like this:

<pre>
[root@kdc ~]# cat /var/kerberos/krb5kdc/kadm5.acl
*/admin@CODINGBEE.NET	*
</pre>


Next we edit the following file:


<pre>
[root@kdc ~]# cat /etc/krb5.conf
# Configuration snippets may be placed in this directory as well
includedir /etc/krb5.conf.d/

[logging]
 default = FILE:/var/log/krb5libs.log
 kdc = FILE:/var/log/krb5kdc.log
 admin_server = FILE:/var/log/kadmind.log

[libdefaults]
 dns_lookup_realm = false
 ticket_lifetime = 24h
 renew_lifetime = 7d
 forwardable = true
 rdns = false
<strong># default_realm = EXAMPLE.COM</strong>
 default_ccache_name = KEYRING:persistent:%{uid}

[realms]
<strong># EXAMPLE.COM = {
#  kdc = kerberos.example.com
#  admin_server = kerberos.example.com
# }</strong>

[domain_realm]
<strong># .example.com = EXAMPLE.COM
# example.com = EXAMPLE.COM</strong>
</pre>

Here we need to activate this commented parts, and once again replace the generic information, i.e. it should look like:

<pre>
[root@kdc ~]#
[root@kdc ~]# cat /etc/krb5.conf
# Configuration snippets may be placed in this directory as well
includedir /etc/krb5.conf.d/

[logging]
 default = FILE:/var/log/krb5libs.log
 kdc = FILE:/var/log/krb5kdc.log
 admin_server = FILE:/var/log/kadmind.log

[libdefaults]
 dns_lookup_realm = false
 ticket_lifetime = 24h
 renew_lifetime = 7d
 forwardable = true
 rdns = false
 default_realm = CODINGBEE.NET
 default_ccache_name = KEYRING:persistent:%{uid}

[realms]
  CODINGBEE.NET = {
   kdc = kdc.codingbee.net
   admin_server = kdc.codingbee.net
}

[domain_realm]
 .codingbee.net = CODINGBEE.NET
 codingbee.net = CODINGBEE.NET

</pre>

Now as part of our hello-world ssh test, we need to create a test user account, so in our example we'll create the following:

<pre>
[root@kdc ~]# useradd krbtest
</pre> 

This user actually needs to be created on the client boxes only. However we're creating it on the kerberos server so that we can do some sanity testing locally. 


Now we're ready to create the Kerberos server's internal database, to do this run:

<pre>
[root@kdc ~]# kdb5_util create -s -r CODINGBEE.NET
</pre>

This command can take several minutes. You need to choose a password when prompted. 

Once that's done, you need to enable and start the following deamons:

<pre>
$ systemctl enable krb5kdc
$ systemctl enable kadmin
$ systemctl start krb5kdc
$ systemctl start kadmin
</pre>



<h2>Configure firewalld to accept Kerberos related traffic</h2>

In terms of firewalld, you just need to add the kerberos and kadmin services:

<pre>
$ systemctl start firewalld.service
$ firewall-cmd --add-service=kerberos --permanent
$ firewall-cmd --add-service=kadmin --permanent
$ systemctl restart firewalld.service
$ systemctl enable firewalld.service
</pre>




<h2>Register trusted entities to the Kerberos Database</h2>
Next we need to start populating the Kerberos Database with entities that makes up the realm. We do this by running the <strong>kadmin.local</strong> which starts an interactive shell:

<pre>
[root@kdc ~]# kadmin.local
Authenticating as principal root/admin@CODINGBEE.NET with password.
kadmin.local:
</pre>

The first entity to add should be the root level kerberos entity, which is done by running the following in the kadmin.local session:

<pre>
kadmin.local: addprinc root/admin
</pre>

Then choose+confirm a password when prompted. Make a note of this password because it is need when (remotely) adding any additional entities in the future. Also notice how we used the 'root/' notation. You can think of this as meaning that we're referring to a table name called 'root' inside the internal kdb database. 

Now we need to register the krbtest user account to the kerberos database, so that it becomes trusted. 

<pre>
kadmin.local:  addprinc krbtest
</pre>

Then choose+confirm a password for this account when prompted. Notice this time we didn't use '{table-name}/' notation. I think that's because when not specified it uses the 'user/' as the default.  

Then add the kerberos server itself as a trusted object into the kerberos database: 

<pre>
kadmin.local:  addprinc -randkey host/kdc.codingbee.net
</pre>

Here, we're adding a new entry to the 'host' table. Next get the keytab file:

<pre>
kadmin.local:  ktadd host/kdc.codingbee.net
</pre>

The ktadd essentially creates the following file in the background:

<pre>
$ file /etc/krb5.keytab
</pre>

Kerberos works using symmetric encryption. This file stores the keys for encryption/decryption.  


Quick tip: to find out what other commands you can run in this interactive shell, run:

<pre>
kadmin.local:  ?
</pre>

Finally do a quick check of what's in the database:

<pre>
kadmin.local:  listprincs
</pre>

Finally quit out of the kadmin interactive session:

<pre>
kadmin.local:  quit
</pre>




<h2>Locally test our setup - configure ssh</h2>
Next we'll configure ssh so that it start's relying on kerberos to do the authentication. This is optional, since in our scenario, we don't need to ssh from any of the client boxes to the Kerberos server. But we are going to do this for early sanity checking purposes only. 

First we'll configure the ssh daemon side config file <code>/etc/ssh/sshd_config</code>. All you need to do is ensure the following line is present:

<pre>
GSSAPIAuthentication yes
</pre>


Then restart the sshd deamon:

<pre>
[root@kdc ~]# systemctl restart sshd
</pre>

Now the sshd deamon will attempt to authenticate using kerberos. However we still need to configure the ssh client side to initiate an kerberos based authentication. To do this we need edit <code>/etc/ssh/ssh_config</code>. Basically the following lines needs to be present:

<pre>
    GSSAPIAuthentication yes
    GSSAPIDelegateCredentials yes
</pre>

These lines are likely to already be present but commented out. If so then need to uncomment them and make sure they are both set to yes. 

Finally we need to apply the following setting:

<pre>
[root@kdc ~]# authconfig --enablekrb5  --update
</pre>


Now we are ready to do a quick sanity check to test our setup. 

First switch to the test user:

<pre>
[root@kdc ~]# su - krbtest
</pre>

Now if we try to ssh into itself, we get a password prompt:

<pre>
[krbtest@kdc ~]$ ssh kdc.codingbee.net
The authenticity of host 'kdc.codingbee.net (127.0.1.1)' can't be established.
ECDSA key fingerprint is SHA256:wkwlYMdcolcOB+zOiw7cMsAb+RKTn1Tim01WJGrZ21Y.
ECDSA key fingerprint is MD5:7c:f6:cc:1a:85:f0:fa:78:02:37:61:27:f3:fb:cc:9e.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'kdc.codingbee.net' (ECDSA) to the list of known hosts.
krbtest@kdc.codingbee.net's password:
</pre>

So far we have failed to ssh in without the password. That's because we don't have an existing token to use. So we need to generate a new token. Before we generate this token, lets first check that there are no active tokens:

<pre>
[krbtest@kdc ~]$ klist
klist: Credentials cache keyring 'persistent:1001:1001' not found
</pre>

To get a token, we first need to initialise kerberos:

<pre>
[krbtest@kdc ~]$ kinit
Password for krbtest@CODINGBEE.NET:  {enter password that you chose when registering krbtest in the krb database}
</pre>

If you entered the correct password, then you don't get any reponse. So to check if this has worked run the klist command again, it should now look something like this:

<pre>
[krbtest@kdc ~]$ klist
Ticket cache: KEYRING:persistent:1001:1001
Default principal: krbtest@CODINGBEE.NET

Valid starting       Expires              Service principal
02/25/2018 19:25:26  02/26/2018 19:25:26  krbtgt/CODINGBEE.NET@CODINGBEE.NET
</pre>

Now if we try again, we it should connect without any password prompt:

<pre>
[krbtest@kdc ~]$ ssh kdc.codingbee.net
Last login: Sun Feb 25 19:21:24 2018
[krbtest@kdc ~]$
</pre>

Success!

<h2><a id='Kerberos-Client-Setup'>Kerberos Client Setup</a></h2>
As part of our example scenario we have 2 client boxes, so we need to repeat the following steps on both boxes in order to get our scenario working. 

To start with, we need to mimic valid fqdns by inserting the following entries into the <code>/etc/hosts</code> file:

<pre>
192.168.10.100 kdc.codingbee.net
192.168.10.101 krb-client1.codingbee.net
192.168.10.102 krb-client2.codingbee.net
</pre>

Next we need to install the necessary rpms:

<pre>
$ yum install krb5-workstation pam_krb5
</pre>

Then edit the <code>/etc/krb5.conf</code>. It needs to look identical to the <code>/etc/krb5.conf</code> file that's in the kerberos server. So simply copy and pasting will do the trick. 

Now run the following command:


<pre>
[root@krb-client1 ~]# kadmin
Authenticating as principal root/admin@CODINGBEE.NET with password.
Password for root/admin@CODINGBEE.NET:
</pre>

Here you'll get prompted to enter the root kerberos password. This is the password you chose when setting up the kerberos server. After that you will get taken to the remote version of the kadmin.local session. Next let's see what entities are registered on the central kerberos database:

<pre>
kadmin:  list_principals
K/M@CODINGBEE.NET
host/kdc.codingbee.net@CODINGBEE.NET
kadmin/admin@CODINGBEE.NET
kadmin/changepw@CODINGBEE.NET
kadmin/kdc.codingbee.net@CODINGBEE.NET
kiprop/kdc.codingbee.net@CODINGBEE.NET
krbtest@CODINGBEE.NET
krbtgt/CODINGBEE.NET@CODINGBEE.NET
root/admin@CODINGBEE.NET
</pre>

At the moment our box isn't registered so let's register it:

<pre>
kadmin:  addprinc -randkey host/krb-client1.codingbee.net
</pre>

This command essentially generates a long random string that's going to be stored on the kerberos database. This is symmetric encryption key, so we need to install a local copy of this key to. We create this local key by generating the keytab file:

<pre>
kadmin:  ktadd host/krb-client1.codingbee.net
</pre>

Now if we list principles:

<pre>
kadmin:  list_principals
K/M@CODINGBEE.NET
host/kdc.codingbee.net@CODINGBEE.NET
<strong>host/krb-client1.codingbee.net@CODINGBEE.NET</strong>
kadmin/admin@CODINGBEE.NET
kadmin/changepw@CODINGBEE.NET
kadmin/kdc.codingbee.net@CODINGBEE.NET
kiprop/kdc.codingbee.net@CODINGBEE.NET
krbtest@CODINGBEE.NET
krbtgt/CODINGBEE.NET@CODINGBEE.NET
root/admin@CODINGBEE.NET
kadmin:
</pre>

Now we can see our recently added entry. Now we exit out:


<pre>
kadmin:  quit
</pre>

Next we configure ssh:


First we'll configure the ssh daemon side config file <code>/etc/ssh/sshd_config</code>. All you need to do is ensure the following line is present:

<pre>
GSSAPIAuthentication yes
</pre>


Then restart the sshd deamon:

<pre>
[root@kdc ~]# systemctl restart sshd
</pre>

Now the sshd deamon will attempt to authenticate using kerberos. However we still need to configure the ssh client side to initiate an kerberos based authentication. To do this we need edit <code>/etc/ssh/ssh_config</code>. Basically the following lines needs to be present:

<pre>
    GSSAPIAuthentication yes
    GSSAPIDelegateCredentials yes
</pre>

These lines are likely to already be present but commented out. If so then need to uncomment them and make sure they are both set to yes. 

Finally we need to apply the following setting:

<pre>
[root@kdc ~]# authconfig --enablekrb5  --update
</pre>


Now you need to repeat the same steps for the krb-client2.codingbee.net box. After that you will be ready to 



<h2>Test the Kerberos setup</h2>
Now we are ready to put our setup to the test. To do this test, log into one of the client boxes. In my case I'll use. krb-client2.codingbee.net 


We first need to create a test user account, so in our example we'll create the following:

<pre>
[root@krb-client1 ~]# useradd krbtest
</pre> 










First switch to the test user:

<pre>
[root@kdc ~]# su - krbtest
</pre>



<pre>
[root@kdc ~]# su - krbtest
</pre>

Now if we try to ssh into itself, we get a password prompt:

<pre>
[krbtest@krb-client1 ~]$ ssh krb-client2.codingbee.net
The authenticity of host 'krb-client2.codingbee.net (192.168.10.102)' can't be established.
ECDSA key fingerprint is SHA256:wkwlYMdcolcOB+zOiw7cMsAb+RKTn1Tim01WJGrZ21Y.
ECDSA key fingerprint is MD5:7c:f6:cc:1a:85:f0:fa:78:02:37:61:27:f3:fb:cc:9e.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'krb-client2.codingbee.net,192.168.10.102' (ECDSA) to the list of known hosts.
krbtest@krb-client2.codingbee.net's password:
</pre>

So far we have failed to ssh in without the password. That's because we don't have an existing token to use, which you can check like this:

<pre>
[krbtest@krb-client1 ~]$ klist
klist: Credentials cache keyring 'persistent:1001:1001' not found
</pre>

To get a token, we first need to initialise kerberos:

<pre>
[krbtest@kdc ~]$ kinit
Password for krbtest@CODINGBEE.NET:  {enter password that you chose when registering krbtest in the krb database}
</pre>

If you entered the correct password, then you don't get any reponse. So to check if this has worked run the klist command again, it should now look something like this:

<pre>
[krbtest@kdc ~]$ klist
Ticket cache: KEYRING:persistent:1001:1001
Default principal: krbtest@CODINGBEE.NET

Valid starting       Expires              Service principal
02/25/2018 19:25:26  02/26/2018 19:25:26  krbtgt/CODINGBEE.NET@CODINGBEE.NET
</pre>
This time we have a token, but not it expires after exactly 24 hours. 

Now if we try again, we it should connect without any password prompt:

<pre>
[krbtest@krb-client1 root]$ ssh krb-client2.codingbee.net
Last login: Sun Feb 25 17:17:20 2018 from 192.168.10.101
[krbtest@krb-client2 ~]$
</pre>

Success!

Note: if skipped doing 'kinit', and instead ran the ssh command directly, then you will get a password prompt. If you then entered the password that you set during the principal adding of the krbtest user, then it will login in successfully and and create the 24 hour token for you. 







<h4>Recommended reading</h4>
http://www.roguelynn.com/words/explain-like-im-5-kerberos/

[post-content post_name=rhsca-quiz] 

In our quiz we'll call our kerberos server as <strong>kdc.codingbee.net</strong> and our kerberos client as krb-<strong>client1.codingbee.net</strong>. 

[accordion]
[toggle title="What are the 2 pre-requisites before setting up Kerberos in an infrastructure?"]
- all boxes must have fqdn (you can mimic this in small environment using /etc/hosts)
- time need to be synced. 
[/toggle]
[toggle title="What are the main steps of setting up a Kerberos server?"]
1. install kerberos rpms
2. edit the kerberos config files
3. create test system account for local testing purposes
4. create the internal kerberos database
5. start+enable the kerberos daemons
6. open up firewall rules
7. enter the kerberos interactive terminal to perform a few task. 
8. configure ssh setup (for local testing purposes)
[/toggle]
[toggle title="What is command to install the necessary rpms on the Kerberos Server?"]
$ yum install krb5-server krb5-workstation pam_krb5
[/toggle]
[toggle title="What files needs to be edited on the Kerberos Server?"]
- /var/kerberos/krb5kdc/kdc.conf
- /var/kerberos/krb5kdc/kadm5.acl
- /etc/krb5.conf
[/toggle]
[toggle title="What parts do you need to change in /var/kerberos/krb5kdc/kdc.conf?"]
- Need to customize the generic example.com statements
- Need to uncomment the 'master_key_type' line
- Insert the following just after the 'master_key_type' line "default_principle_flags = +preauth"
[/toggle]
[toggle title="What part do you need to change in /var/kerberos/krb5kdc/kadm5.acl?"]
- Need to customize the generic example.com statements
[/toggle]
[toggle title="What part do you need to change in /etc/krb5.conf?"]
Just need to uncomment the generic example.com sections and then customise them. 
[/toggle]
[toggle title="What is the command to create test user account 'krbtest'?"]
$ useraadd krbtest
[/toggle]
[toggle title="What's the command to create the internal kdc database?"]
$ kdb5_util create -s -r CODINGBEE.NET
[/toggle]
[toggle title="What is the command to enable and start the kerberos daemons?"]
# enable and start the Kerberos daemons:
$ systemctl enable krb5kdc
$ systemctl enable kadmin
$ systemctl start krb5kdc
$ systemctl start kadmin
[/toggle]
[toggle title="What is the command to open up the firewall rules?"]
# open up firewalls:
$ firewall-cmd --add-service=kerberos --permanent
$ firewall-cmd --add-service=kadmin --permanent
$ systemctl restart firewalld.service
[/toggle]
[toggle title="What is the command to enter the kerberos interactive terminal?"]
$ kadmin.local
[/toggle]
[toggle title="What tasks needs to performed in the kerberos interactive session?"]
- set a password for the main admin account
- register system test user account
- register host machine
- pull down local copies of the symmetric files
[/toggle]
[toggle title="What is the command to set a password for the main admin account?"]
kadmin.local: addprinc root/admin
[/toggle]
[toggle title="What is the command to register system test user account?"]
kadmin.local:  addprinc krbtest
# also need to choose+set a password when prompted. 
[/toggle]
[toggle title="What is the command to register host machine?"]
kadmin.local:  addprinc -randkey host/kdc.codingbee.net
[/toggle]
[toggle title="What is the command to pull down copies of symmetric key files?"]
kadmin.local:  ktadd host/kdc.codingbee.net
[/toggle]
[toggle title="What is the command to view help info?"]
kadmin.local:  ?
[/toggle]
[toggle title="What is the command to exit out of the kdc terminal?"]
kadmin.local:  quit
[/toggle]
[toggle title="Where is the symmetric key file stored?"]
/etc/krb5.keytab
[/toggle]
[toggle title="Which ssh config file needs to be changed?"]
- /etc/ssh/sshd_config
- /etc/ssh/ssh_config
[/toggle]
[toggle title="What setting needs to be present in the /etc/ssh/sshd_config file?"]
GSSAPIAuthentication yes
[/toggle]
[toggle title="What setting needs to be present in the /etc/ssh/ssh_config file?"]
GSSAPIAuthentication yes
GSSAPIDelegateCredentials yes
# These lines are already present but just needs to be uncommented.
[/toggle]
[toggle title="What command needs to be run in order to load in these ssh config changes?"]
$ systemctl restart sshd
[/toggle]
[toggle title="what is the command to enable krb5 based authentication?"]
$ authconfig --enablekrb5  --update
[/toggle]
[toggle title="What are the steps you need to take to locally test this?"]
- switch to the test account (krbtest)
- run kinit
- check kinit has worked by checking token exist by running klist
- ssh to itself, as the krbtest user.  
[/toggle]
[toggle title="What are the steps you need to do to set up a krb client?"]
- install the rpms
- update krb config file, there is only one file, which is /etc/krb5.conf
- enter kdc interactive session and perform a few tasks
- configure ssh to use kdc authentication
- test the client setup
[/toggle]
[toggle title="what is the command to install the client rpms needs to be installed on the client?"]
$ yum install krb5-workstation pam_krb5
[/toggle]
[toggle title="What is the command to enter the kerberos interactive session?"]
$ kadmin
[/toggle]
[toggle title="what tasks do you need to run in the kerberos session?"]
- list what resources exist. 
- register the user account (if it hasn't already been registered)
- register the host
- pull down the symmetric keys
[/toggle]
[toggle title="what is the command to list resources?"]
kadmin:  list_principals
[/toggle]
[toggle title="What is the command to register the client?"]
kadmin:  addprinc -randkey host/krb-client1.codingbee.net
[/toggle]
[toggle title="What is the command to pull down the symmetric keys?"]
kadmin:  ktadd host/krb-client1.codingbee.net
[/toggle]
[toggle title="What ssh related configurations needs to be made?"]
Perform the same sshd config file changes as done on the kerberos server
[/toggle]
[toggle title="what is the command to load and activate these changes:"]
$ systemctl restart sshd
$ authconfig --enablekrb5  --update
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[toggle title="question?"]
answer
[/toggle]
[/accordion]