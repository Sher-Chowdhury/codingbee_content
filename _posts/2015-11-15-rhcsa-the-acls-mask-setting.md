---
ID: 486
post_title: 'The ACL&#8217;s mask setting'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-the-acls-mask-setting
published: true
post_date: 2015-11-15 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What are the 2 commands you can use for setting mask values?"]
- chmod
- setfacl
[/toggle]
[toggle title="What is the command that indirectly applies the mask, '---', to the file /tmp/testfile.txt?"]
$ chmod 000 /tmp/testfile.txt 	 
[/toggle]
[toggle title="What is the command to explictly apply the mask, 'r-x'?, to the file /tmp/testfile.txt?"]
$ setfacl -m m<mark>::</mark>r-x /tmp/testfile.txt
# notice the double colon syntax. 
[/toggle]
[/accordion]

<hr/>




In the previous example we had:
<pre>$ getfacl /tmp/testfile.txt
getfacl: Removing leading '/' from absolute path names
# file: tmp/testfile.txt
# owner: root
# group: root
user::rw-
user:homer:rw-
group::r--
<mark>mask::rw-</mark>
other::r--
</pre>
Here we have the "mask" setting. This acts as safety-mechanism. To understand how this works, lets first create a few more users:
<pre>
$ useradd bart	 
$ useradd marge	 
$ useradd lisa	 
$ useradd maggie
</pre>	 
Now let's all give them various special permissions to /tmp/testfile.txt:	
<pre>	 
$ setfacl -m u:homer:rwx /tmp/testfile.txt	
$ setfacl -m u:marge:rwx /tmp/testfile.txt	
$ setfacl -m u:lisa:rw- /tmp/testfile.txt	
$ setfacl -m u:bart:r-- /tmp/testfile.txt	
$ setfacl -m u:maggie:r-- /tmp/testfile.txt	
</pre>	 	
Now our special permissions for /tmp/testfile.txt should now look something like this:	
<pre>	 
$ getfacl /tmp/testfile.txt
getfacl: Removing leading '/' from absolute path names
# file: tmp/testfile.txt
# owner: root	
# group: root	
user::r--	 	
user:homer:rwx	
user:bart:r--	
user:marge:rwx	
user:lisa:rw-	
user:maggie:r--	
group::r--	 	
mask::rwx	 	
other::r--	 	
</pre>	 
Now what if you suspect that /tmp/testfile.txt might contain a virus! In this situation you might want to delete all the special permissions (using the --remove-all option) so that none of these might accidentally trigger the virus. However removing all the special persmissions isn't a good approach. That's because after inspecting the testfile.txt, if you discover that this file is safe after all, then you need to re-apply all the special permissions again. That's why a better option is to lock down the file but at same time preserve the special permissions settings (in a suppressed mode) to be reactivated in future again. This is made possible using the "mask" setting. You can apply it implicitly by simply using the chmod command:
<pre>	
$ chmod 000 /tmp/testfile.txt 	 
</pre>	

This results in a bunch of "effective" settings:	

<pre>
$ getfacl /tmp/testfile.txt	 
getfacl: Removing leading '/' from absolute path names	
# file: tmp/testfile.txt
# owner: root	
# group: root	
user::---
user:homer:rwx    #effective:---	
user:bart:r--     #effective:---	
user:marge:rwx    #effective:---	
user:lisa:rw-     #effective:---	
user:maggie:r--   #effective:---	
group::r--        #effective:---	 	
mask::---	
other::---	
</pre>

The mask setting is set to the maximum allowed setting for all users. This "effectively" override special permissions. 

The mask setting will automatically update again indirectly when you modify permissions using either the chmod or setfacl command. But you can also directly change the mask setting as well. E.g. if you wan to set the mask to "r-x", then you do:

<pre>
$ setfacl -m m::r-x /tmp/testfile.txt
</pre> 

Notice that we have "::" since it is empty as the mask setting is not something particular to a user or group.