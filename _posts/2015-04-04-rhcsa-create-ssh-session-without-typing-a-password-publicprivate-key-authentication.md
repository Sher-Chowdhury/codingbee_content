---
ID: 343
post_title: 'RHCSA &#8211; Create SSH session without typing a password (Public/Private Key Authentication)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-create-ssh-session-without-typing-a-password-publicprivate-key-authentication
published: true
post_date: 2015-04-04 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="01. Assume you are logged in as the user 'tom' on machine called 'LinuxA'. Assume there is another user account called 'tom' but on machine 'LinuxB'. what is the command to ssh into machine 'LinuxB' as the user tom (also assume private/public keys have not been set up yet)?"]
[tom@LinuxA /]$ ssh linuxB      
# you will then be prompted for password
[/toggle]
[toggle title="02. Assume LinuxB also has the user 'jerry'. In that case what is the command to ssh into the user 'jerry' instead of the user 'tom' (also assume private/public keys have not been set up yet)?"]
[tom@LinuxA /]$ ssh jerry@linuxB
# you will then be prompted for password
[/toggle]
[toggle title="03. What is the command to generate public/private keys?"]
$ ssh-keygen
[/toggle]
[toggle title="04. What files will the above command create?"]
<pre>
- ~/.ssh/id_rsa     # You can think of this file as a key.
- ~/.ssh/id_rsa.pub # You can think of this file as a padlock.
</pre>
[/toggle]
[toggle title="05. What is the command to copy the public key to user jerry on LinuxB from user tom?"]
[tom@LinuxA ~]$ ssh-copy-id jerry@LinuxB
[/toggle]
[toggle title="06. What file will get appended with the public key on the jerry user's account?"]
/home/jerry/.ssh/authorized_keys
[/toggle]
[toggle title="07. Assume you added a passphrase during the ssh-keygen command. What is the command to enable passphrase caching mode?"]
[tom@LinuxA ~]$ ssh-agent bash 
[/toggle]
[toggle title="08. What is the command, to add your passphrase to the cache?"]
[tom@LinuxA ~]$ ssh-add   # you'll then be prompted to add it to cache
[/toggle]
[/accordion]

<hr/>



If you are logged into one linux machine (lets call it LinuxA), but you now want to connect to another Linux machine (lets call it LinuxB) from LinuxA, then it is possible to do this using the "ssh" command. Here's an example of how this is done

<pre>
[Tom@LinuxA /]$ ssh linuxB
</pre> 


When you use ssh, you need to log in as a user that already exists on LinuxB. In the above case you will get 2 prompts, the first to enter a LinuxB username, followed by that user account's password. However you can reduce this to 1 prompt by specifying the username as part of the initial connection request:

<pre>
[Tom@LinuxA /]$ ssh Jerry@linuxB
</pre> 

This time, you only get prompted once, which is to enter the password for user "Jerry". 


Here I am logged in as user "Tom" on LinuxA but when I ssh'd into LinuxB I assumed the identity of a user called "Jerry". In order to establish the connection, I had to enter jerry's password. 

Once I have logged into LinuxB as the "Jerry" user, I can do anything i want on LinuxB as if you have logged into LinuxB directly as the "Jerry" user.


However it is also possible to suppress the password prompt request as well. This is done by setting up something that's called private/public key authentication.

Private/public keys are essentially a pair of files that resides in the user's home directory, in the .ssh folder. These 2 files are:


<ul>
	<li>~/.ssh/id_rsa        # You can think of this file as a key.</li>
	<li>~/.ssh/id_rsa.pub    # You can think of this file as a padlock.</li>
</ul>


If these files don't exist, or in fact the .ssh folder itself doesn't exist then it means that no public/private keys has not been created for this user yet. In which case you need to generate these keys.


<h2>Generate Private/Public Keys</h2>

To generate public/private keys you simply have to run the following command
    
<pre>$ ssh-keygen</pre>

This command will prompt you to enter some info, but you can simply hit return to all of them to accept the defaults. 

Here's what you'll see happening when you run the command:

<pre>
[tom@localhost ~]$ pwd
/home/tom
[tom@localhost ~]$ ls -la | grep .ssh
[tom@localhost ~]$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/tom/.ssh/id_rsa):
Created directory '/home/tom/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/tom/.ssh/id_rsa.
Your public key has been saved in /home/tom/.ssh/id_rsa.pub.
The key fingerprint is:
7e:78:9e:96:0b:94:d1:20:88:a2:b0:40:d4:92:2e:dc tom@localhost.localdomain
The key's randomart image is:
+--[ RSA 2048]----+
|ooo. .. .        |
|+o...  . o       |
|*oo     . .      |
|+o E     o       |
|.       S        |
|       o .       |
|        + o.     |
|         =o.     |
|         .+.     |
+-----------------+
[tom@localhost ~]$ ls -la | grep .ssh
drwx------. 2 tom  tom    36 Apr  6 13:55 .ssh
[tom@localhost ~]$ cd .ssh/
[tom@localhost .ssh]$ ls -la
total 12
drwx------. 2 tom tom   36 Apr  6 13:55 .
drwx------. 6 tom tom 4096 Apr  6 13:55 ..
-rw-------. 1 tom tom 1675 Apr  6 13:55 id_rsa
-rw-r--r--. 1 tom tom  407 Apr  6 13:55 id_rsa.pub
[tom@localhost .ssh]$

</pre>

As you can see that this command created the .ssh folder, it then generated the id_rsa and id_rsa.pub keys and placed them in this folder.

Now we need to tell the "Jerry" user to accept the "id_rsa.pub" as an "authorised" padlock that a user (in this case Tom) is allowed to unlock (using the id_rsa file) in order to login as the Jerry user. This is done by updating the Jerry user's authorized_keys file. 


<h2>Adding the id_rsa.pub to the authorized_keys file using the ssh-copy-id command</h2> 
We now need to make Jerry aware of the existence of the padlock (id_rsa.pub) as an authorised key that the Jerry user will publicly present this padlock to any incoming ssh requests. This is done by simply appending the id_rsa.pub file's content's to the Jerry user's authorized_keys file, which resides in the Jerry user's .ssh folder:


<pre>[jerry@localhost .ssh]$ pwd
/home/jerry/.ssh
[jerry@localhost .ssh]$ ls -l authorized_keys
-rw-------. 1 jerry jerry 815 Apr  6 14:12 authorized_keys
[jerry@localhost .ssh]$

</pre>

Note, this file might not exist yet, in which case you can manually create this file and copy+paste the id_rsa.pub into it. However doing this manually is quite error prone, which is why there is the <code>ssh-copy-id</code> command which is specifically used for this purpose. For example, here how to add Tom's id_rsa.pub file's content to Jerry's authorized_keys file:

<pre>
[tom@localhost ~]$ ssh-copy-id jerry@localhost
/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
jerry@localhost's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'jerry@localhost'"
and check to make sure that only the key(s) you wanted were added.

[tom@localhost ~]$
</pre>


Note, this command does prompt you for Jerry's password. 

Now you can ssh without a password prompt:


<pre>
[tom@localhost ~]$ ssh jerry@localhost
[jerry@localhost ~]$</pre>



<h2>Assymetric encryption</h2>
SSH using <a href="http://codingbee.net/cookbooks/centos/openssl-demo-encrypting-decrypting-files-using-both-symmetric-and-asymmetric-encryption">asymmetric encryption</a> as opposed to symmetric encryption. 



<h2>Using the Passphrase</h2>

When running the <code>ssh-keygen</code> command to generate your public+private keys you get prompted to enter a "passphrase". This passphrase is used to create an encrypted private key. 

After that you will need to enter this passphrase everytime you want to ssh into a remote server. This at first sounds like it defeats the whole point of public+private keys, and especially makes running shell scripts impossible. But actually you can cache this passphrase into your terminal session, by running the following command:

<pre>
$ ssh-agent bash 
</pre>

This basically starts a new bash session but this time with the ssh-agent utility enabled. 

In our new bash session, we now use the ssh-add command to add our passphrase to the ssh-agent's cache memory:

<pre>
[root@puppetmaster ~]$ ssh-add
Enter passphrase for /root/.ssh/id_rsa:
Identity added: /root/.ssh/id_rsa (/root/.ssh/id_rsa)
[root@puppetmaster ~]$
[root@puppetmaster ~]$ ssh root@puppetagent01
Last login: Mon Oct 12 21:57:12 2015 from puppetmaster
[root@puppetagent01 ~]$
</pre>
  
This time we didn't get a password prompt. If we now exit out of our bash terminal, then ssh to the remote machine again, then we'll get a password prompt:

<pre>
[root@puppetmaster ~]# exit
exit
[root@puppetmaster ~]# ssh root@puppetagent01
Enter passphrase for key '/root/.ssh/id_rsa':
Last login: Mon Oct 12 22:09:24 2015 from puppetmaster
[root@puppetagent01 ~]#
</pre>









 


<strong>Public key / private key authentication</strong>


http://www.cyberciti.biz/faq/how-to-set-up-ssh-keys-on-linux-unix/
http://www.cyberciti.biz/faq/install-ssh-identity-key-remote-host/ 






See also:
https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/s2-ssh-configuration-keypairs.html
&nbsp;

http://nerderati.com/2011/03/17/simplify-your-life-with-an-ssh-config-file/
http://linux.die.net/man/5/ssh_config
https://blogs.aws.amazon.com/security/post/Tx3N8GFK85UN1G6/Securely-connect-to-Linux-instances-running-in-a-private-Amazon-VPC


bastion hosts:
https://10mi2.wordpress.com/2015/01/14/using-ssh-through-a-bastion-host-transparently/
http://stackoverflow.com/questions/7114990/pseudo-terminal-will-not-be-allocated-because-stdin-is-not-a-terminal  #use the -t option