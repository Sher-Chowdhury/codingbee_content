---
ID: 3341
post_title: >
  RHCE – Create a hello world CGI app on
  CentOS/RHEL 7
author: sher
post_excerpt: >
  This is walktrhough how to generate ssl
  certificates and then configure https on
  a CentOS 7 Apache server to use the ssl
  certificate.
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/rhce-create-a-hello-world-cgi-app-on-centos-rhel-7
published: true
post_date: 2018-03-31 12:50:07
---
Common Gateway Interface (CGI) is a protocol that let's you run custom scripts via the web. It's not as commonly used as before, but you still need to know this as part of the RHCE exam objectives. 



In Apache, there is a default folder where you can place scripts in, which will then get handled by the CGI protocol. This default folder is declared in the main Apache config file, here's the relevant extract:

  

 
<pre>
<IfModule alias_module>
    #
    # Redirect: Allows you to tell clients about documents that used to
    # exist in your server's namespace, but do not anymore. The client
    # will make a new request for the document at its new location.
    # Example:
    # Redirect permanent /foo http://www.example.com/bar

    #
    # Alias: Maps web paths into filesystem paths and is used to
    # access content that does not live under the DocumentRoot.
    # Example:
    # Alias /webpath /full/filesystem/path
    #
    # If you include a trailing / on /webpath then the server will
    # require it to be present in the URL.  You will also likely
    # need to provide a <Directory> section to allow access to
    # the filesystem path.

    #
    # ScriptAlias: This controls which directories contain server scripts.
    # ScriptAliases are essentially the same as Aliases, except that
    # documents in the target directory are treated as applications and
    # run by the server when requested rather than as documents sent to the
    # client.  The same rules about trailing "/" apply to ScriptAlias
    # directives as to Alias.
    #
<strong>    ScriptAlias /cgi-bin/ "/var/www/cgi-bin/"</strong>

</IfModule>
</pre> 





Here we can see the <a href="https://httpd.apache.org/docs/2.4/mod/mod_alias.html#scriptalias" target="_blank" rel="nofollow">ScriptAlias</a> setting. This setting maps the part of a URL to a directory path on the machine. We can drop scripts into /var/www/cgi-bin/, and then we should be able to access them using the '/cgi-bin/' alias in the url, e.g. something like http://example.com/cgi-bin/test.sh

We now need to check that the cgi module is running:


<pre>
[root@webserver cgi-bin]# httpd -M | grep cgi
 proxy_fcgi_module (shared)
 proxy_scgi_module (shared)
<strong> cgi_module (shared)</strong>
 fcgid_module (shared)
</pre>

Since our box is in SELinux enforcing mode, Theres a SE boolean that needs to be enabled to allow the use of CGI scripts:

<pre>
[root@webserver cgi-bin]# getsebool -a | grep -i cgi
git_cgi_enable_homedirs --> off
git_cgi_use_cifs --> off
git_cgi_use_nfs --> off
<strong>httpd_enable_cgi --> on</strong>
</pre>

In case it wasn't enable then you can enable it (p)ersistantly by running:

<pre>
$ setsebool -P httpd_enable_cgi 1
</pre>

Now to try out CGI, I created the following hello world bash script:


<pre>
[root@webserver cgi-bin]# ls -lZ /var/www/cgi-bin/test.sh
-rwxr-xr--. apache apache unconfined_u:object_r:httpd_sys_script_exec_t:s0 /var/www/cgi-bin/test.sh
[root@webserver cgi-bin]# cat /var/www/cgi-bin/test.sh
#!/bin/bash
echo 'Content-type: text'
echo
echo
echo 'hello world'
echo
echo
</pre>

We added the Content-Type setting so that the web browser know hot to interpret the content. Also notice that the script needs to have a specific security type attribute 'httpd_sys_script_exec_t'. 


Now let's test out this script in the bash terminal first:


<pre>
[root@webserver cgi-bin]# /var/www/cgi-bin/test.sh
Content-type: text


hello world


</pre>


Now you can try accessing this link using firefox/chrome/curl, e.g.:


<pre lang='bash'>
[root@webserver cgi-bin]# curl http://localhost/cgi-bin/test.sh

hello world


</pre>


<h2>Using a non standard CGI folder</h2>

If you want to change the CGI folder to a different location, then you need to take 4 steps:

<ol>
	<li>Create the new folder, and give Apache ownership of this folder</li>
	<li>Place scripts into the new folder</li>
	<li>Update SELinux configurations to support the new cgi script location</li>
	<li>Update the main Apache config, or create/edit vhost file</li>
	<li>Restart httpd daemon</li>
</ol>


Now let's create the new cgi folder:

<pre lang='bash'>
[root@webserver cgi-bin]# mkdir /var/web_scripts
</pre>

Next we create a dummy script and :

<pre lang='bash'>
[root@webserver cgi-bin]# cat /var/web_scripts/test.sh
#!/bin/bash
echo 'Content-type: text'
echo
echo
echo 'hello world - this time we are in non-standard folder'
echo
echo
</pre>

Next we check if the ownership, permissions, and security context looks correct:

<pre lang='bash'>
[root@webserver cgi-bin]# ls -lZ /var/ | grep web_scripts
drw-r--r--. root root unconfined_u:object_r:var_t:s0   web_scripts
[root@webserver cgi-bin]# ls -lZ /var/web_scripts/test.sh
-rwxr-xr--. root root unconfined_u:object_r:var_t:s0   /var/web_scripts/test.sh
</pre>

To start with we need to fix the ownership:


<pre lang='bash'>
[root@webserver cgi-bin]# chown -R apache:apache /var/web_scripts/
</pre>

Next the script needs to be executable:

<pre>
chmod ug+x /var/web_scripts/test.sh
</pre>

Next we need to fix the SELinux type security attribute setting, first find an exmaple of what the setting should be:


<pre lang='bash'>
[root@webserver cgi-bin]# ll -lZ /var/www/ | grep cgi
drwxr-xr-x. root root system_u:object_r:<strong>httpd_sys_script_exec_t</strong>:s0 cgi-bin

</pre> 

Now we create and apply the SELinux policy:

<pre lang='bash'>
[root@webserver cgi-bin]# semanage fcontext -at httpd_sys_script_exec_t "/var/web_scripts(/.*)?"
[root@webserver cgi-bin]# restorecon -Rv /var/web_scripts
restorecon reset /var/web_scripts context unconfined_u:object_r:var_t:s0->unconfined_u:object_r:httpd_sys_script_exec_t:s0
restorecon reset /var/web_scripts/test.sh context unconfined_u:object_r:var_t:s0->unconfined_u:object_r:httpd_sys_script_exec_t:s0
</pre>


Then confirm that this has worked:

<pre lang='bash'>
[root@webserver cgi-bin]# ls -lZ /var/ | grep web_scripts
drwxr-xr-x. apache apache unconfined_u:object_r:httpd_sys_script_exec_t:s0 web_scripts
[root@webserver cgi-bin]# ls -lZ /var/web_scripts
-rwxr-xr--. apache apache unconfined_u:object_r:httpd_sys_script_exec_t:s0 test.sh
</pre>

Now we make Apache aware of our new folder. We can either update the main config file, or edit/create a vhost file. We've already covered other examples where we used vhosts, so this time we'll edit the main Apache config file. SO we made a duplicate of the original ScriptAlias line and commented out the original line and modified the duplicate so that we end up with:


<pre lang='bash'>
    #ScriptAlias /cgi-bin/ "/var/www/cgi-bin/"
    ScriptAlias /cgi-bin/ "/var/web_scripts"
</pre> 

We also have to add in a new Directory directive to override the high level deny '/' directive, so we insert the following extract:

<pre lang='bash'>
    <Directory "/var/web_scripts">
        AllowOverride None
        Options None
        Require all granted
    </Directory>
</pre>


Then we restarted httpd:


<pre lang='bash'>
[root@webserver web_scripts]# systemctl restart httpd
</pre>

Now everything should be working, so let's test with Firefox/Chrome/elinks/curl:


<pre lang='bash'>
[root@webserver web_scripts]# curl http://localhost/cgi-bin/test.sh

hello world - this time we are in non-standard folder


</pre>

Success!

[post-content post_name=rhsca-quiz] 

[accordion]
[toggle title="Where can you find out where is the cgi folder located?"]
See ScriptAlias setting under the main Apache config file. 
[/toggle]
[toggle title="What is the command to check if the cgi module is installed?"]
$ httpd -M | grep cgi
[/toggle]
[toggle title="What is the command to view SELinux boolean settings for cgi?"]
$ getsebool -a | grep cgi
[/toggle]
[toggle title="What is the command to persistantly enable cgi in se boolean settings?"]
$ setsebool -P httpd_enable_cgi on
[/toggle]
[toggle title="Give an example of a hello world cgi script?"]
$ cat /var/www/cgi-bin/test.sh
#!/bin/bash
echo 'Content-type: text'
echo
echo
echo 'hello world'
echo
echo
[/toggle]
[toggle title="What is the command to call this cgi script using curl?"]
$ curl http://localhost/cgi-bin/test.sh
[/toggle]
[toggle title="What are the steps needed to change the default cgi folder, e.g. change it to /var/cgi?"]
- create the new folder and give apache ownership 
- place scripts in the new folder
- sort up selinux file security attribute settings. 
- update the main config file - need to update two sections
- restart apache daemon 
- then test using curl
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