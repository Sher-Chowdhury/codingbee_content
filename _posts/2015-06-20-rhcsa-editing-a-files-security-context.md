---
ID: 427
post_title: 'SELinux &#8211; Editing a file&#8217;s Security Context'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-editing-a-files-security-context
published: true
post_date: 2015-06-20 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to give you an insight in how SELinux determines the security context for all your files+folders?"]
$ semanage fcontext -l
[/toggle]
[toggle title="What are the 2 steps to editing a file's SELinux context?"]
1. First update the target policy itself
2. Then you apply the updated policy to your files and folders.
[/toggle]
[toggle title="What is the command to view the semanage command's subcommands?"]
$ semanage <span style='letter-spacing'>-</span>-help
[/toggle]
[toggle title="What is the command, to view each of these subcommand's man pages?"]
$ man semanage-{subcommand}
[/toggle]
[toggle title="Which subcommand is used for editing the SELinux file mapping definition?"]
fcontext
[/toggle]
[toggle title="What is the command to view this command's man page?"]
$ man semanage-fcontext
[/toggle]
[toggle title="What is the command to view the fcontext subcommand's help info?"]
$ semanage fcontext <span style='letter-spacing'>-</span>-help
[/toggle]
[toggle title="Where can you find examples commands for editing a file's targeted policy?"]
$ man semanage-fcontext
[/toggle]
[toggle title="What is the command to apply the updated policy to the file /tmp/testfile.txt?"]
$ restorecon /tmp/testfile.txt
[/toggle]
[toggle title="How do you refresh all files and folders contexts across the whole machine?"]
$ touch /.autorelabel    
# then reboot the machine. 
[/toggle]
[/accordion]

<hr/>


When you create a new file or folder, then SELinux will automatically assign a value to it. The <code>semanage fcontext -l</code> command gives info about how SELinux determines what security context it needs to assign to all your machine's files and folders:

<pre>
<span style="font-size:10px">$ semanage fcontext -l | wc -l
5846
$ semanage fcontext -l
/etc/cron\.(daily|weekly)/sysklogd    regular file       system_u:object_r:logrotate_exec_t:s0
/etc/cron\.d(/.*)?                    all files          system_u:object_r:system_cron_spool_t:s0
/etc/cron\.daily/[sm]locate           regular file       system_u:object_r:locate_exec_t:s0
/etc/cron\.daily/calamaris            regular file       system_u:object_r:calamaris_exec_t:s0
/etc/cron\.daily/certwatch            regular file       system_u:object_r:certwatch_exec_t:s0
/etc/cron\.daily/prelink              regular file       system_u:object_r:prelink_cron_system_exec_t:s0
/etc/cron\.monthly/proftpd            regular file       system_u:object_r:ftpd_exec_t:s0
/etc/cron\.weekly/(c)?fingerd         regular file       system_u:object_r:fingerd_exec_t:s0
/etc/crontab                          regular file       system_u:object_r:system_cron_spool_t:s0
/etc/ctdb/events\.d/.*                regular file       system_u:object_r:bin_t:s0
/etc/cups(/.*)?                       all files          system_u:object_r:cupsd_etc_t:s0
/etc/cups/certs                       directory          system_u:object_r:cupsd_rw_etc_t:s0
/etc/cups/certs/.*                    regular file       system_u:object_r:cupsd_rw_etc_t:s0
/etc/cups/classes\.conf.*             regular file       system_u:object_r:cupsd_rw_etc_t:s0
/etc/cups/client\.conf                regular file       system_u:object_r:etc_t:s0
.
.
...etc.<span style="font-size=10px">
</pre>
Note, the "(/.*)?" means any child elements will inherit the items security context automatically. 

This command essentially gives the definitive info about what SELinux context every single file and folder must have on your machine. The semanage command retrieves info from the particular policy type that's currently active (which is usually the targeted policy type).  


However when you install an rpm package, the rpm in most cases assign security attribute values to all it’s objects, but for those it doesn’t the policy type will take over. In most cases the given objects values are assigned based on inheritance. E.g. when a user starts a new process, then that new process inherits the user’s security attributes, or if a file is created, then that file inherits of the attributes of the folder it resides in.

<h2>Edit a file's security context using Semanage</h2>

Now if you want a file/folder to have a different security context than what's defined by <code>semanage fcontext -l</code>, then the best way to do this is to first modify the targeted policy itself (using the semanage command). This will mean your changes become part of the source information and hence will survive a reboot (or even a relabel, covered later). After that targeted policy is updated, the next part is to apply the new/updated policy rule to the file/folder. To summarise:  

<ol>
	<li>First update the target policy itself</li>
	<li>Then you apply the updated policy to your files and folders.</li>
</ol>



Before we show how you edit one of the targeted policies listed in <code>semanage fcontext -l</code>, let's first explore semanage itself because it is a really powerful and feature rich command.  Here's the help info for the semanage command:


<pre>
semanage -h
usage: semanage [-h]

                {import,export,login,user,port,interface,module,node,fcontext,boolean,permissive,d
                ...

semanage is used to configure certain elements of SELinux policy with-out
requiring modification to or recompilation from policy source.

positional arguments:
  {import,export,login,user,port,interface,module,node,fcontext,boolean,permissive,dontaudit}
    import              Output local customizations
    export              Output local customizations
    login               Manage login mappings between linux users and SELinux
                        confined users
    user                Manage SELinux confined users (Roles and levels for an
                        SELinux user)
    port                Manage network port type definitions
    interface           Manage network interface type definitions
    module              Manage SELinux policy modules
    node                Manage network node type definitions
    fcontext            Manage file context mapping definitions
    boolean             Manage booleans to selectively enable functionality
    permissive          Manage process type enforcement mode
    dontaudit           Disable/Enable dontaudit rules in policy

optional arguments:
  -h, --help            show this help message and exit



</pre>
 
As you can see, semanage has about a dozen sub commands. Each subcommand has it's own man page, which you can access like this:

<pre>
$ man semanage-{subcommand}
</pre>

e.g. 

<pre>
$ man semanage-fcontext
</pre>


In our case we are interested in the fcontext subcommand:

<pre>
$ semanage fcontext -h
usage: semanage fcontext [-h] [-n] [-N] [-S STORE] [ --add ( -t TYPE -f FTYPE -r RANGE -s SEUSER | -e EQUAL ) FILE_SPEC ) | --delete ( -t TYPE -f FTYPE | -e EQUAL ) FILE_SPEC ) | --deleteall  | --extract  | --list -C | --modify ( -t TYPE -f FTYPE -r RANGE -s SEUSER | -e EQUAL ) FILE_SPEC ) ]

positional arguments:
  file_spec             file_spec

optional arguments:
  -h, --help            show this help message and exit
  -C, --locallist       List fcontext local customizations
  -n, --noheading       Do not print heading when listing fcontext object
                        types
  -N, --noreload        Do not reload policy after commit
  -S STORE, --store STORE
                        Select an alternate SELinux Policy Store to manage
<mark>  -a, --add             Add a record of the fcontext object type
  -d, --delete          Delete a record of the fcontext object type
  -m, --modify          Modify a record of the fcontext object type
  -l, --list            List records of the fcontext object type
</mark>  -E, --extract         Extract customizable commands, for use within a
                        transaction
  -D, --deleteall       Remove all fcontext objects local customizations
  -e EQUAL, --equal EQUAL
                        Substitute target path with sourcepath when generating
                        default label. This is used with fcontext. Requires
                        source and target path arguments. The context labeling
                        for the target subtree is made equivalent to that
                        defined for the source.
  -f {a,f,d,c,b,s,l,p}, --ftype {a,f,d,c,b,s,l,p}
                        File Type. This is used with fcontext. Requires a file
                        type as shown in the mode field by ls, e.g. use -d to
                        match only directories or -- to match only regular
                        files. The following file type options can be passed:
                        -- (regular file),-d (directory),-c (character
                        device), -b (block device),-s (socket),-l (symbolic
                        link),-p (named pipe) If you do not specify a file
                        type, the file type will default to "all files".
  -s SEUSER, --seuser SEUSER
                        SELinux user name
  -t TYPE, --type TYPE  SELinux Type for the object
  -r RANGE, --range RANGE
                        MLS/MCS Security Range (MLS/MCS Systems only) SELinux
                        Range for SELinux login mapping defaults to the
                        SELinux user record range.
</pre>  




This indicates that we can add/modify/delete the targeted policies with semanage's fcontext sub command. 

The semanage fcontext's man page actually contains examples on how to do this:


<pre>
$ man semanage-fcontext | grep -A16 "EXAMPLE"
EXAMPLE
       remember to run restorecon after you set the file context
       Add file-context for everything under /web
       # semanage fcontext -a -t httpd_sys_content_t "/web(/.*)?"
       # restorecon -R -v /web

       Substitute /home1 with /home when setting file context
       # semanage fcontext -a -e /home /home1
       # restorecon -R -v /home1

       For home directories under top level directory, for example /disk6/home,
       execute the following commands.
       # semanage fcontext -a -t home_root_t "/disk6"
       # semanage fcontext -a -e /home /disk6/home
       # restorecon -R -v /disk6
</pre>


Now lets try this out, first let's create a file:


<pre>
$ touch /var/www/html/hello.html
$ ls -lZ /var/www/html/hello.html
-rw-r--r--. root root unconfined_u:object_r:httpd_sys_content_t:s0 /var/www/html/hello.html
</pre>

Now let's say we want to change the type security attribute to "admin_home_t", then based on the man page's example, we do:


<pre>
$ semanage fcontext -a -t admin_home_t /var/www/html/hello.html
</pre>

So now we have added a new targets policy to the fcontext catalog:

<pre>
$ semanage fcontext -l | grep hello.html
/var/www/html/hello.html       all files          system_u:object_r:admin_home_t:s0
</pre>

Now to apply this policy rule to the file, we need to use the <code>restorecon</code> command:

<pre>
$ ls -lZ /var/www/html/hello.html
-rw-r--r--. root root unconfined_u:object_r:<strong>httpd_sys_content_t</strong>:s0 /var/www/html/hello.html
$ restorecon /var/www/html/hello.html
$ ls -lZ /var/www/html/hello.html
-rw-r--r--. root root unconfined_u:object_r:<strong>admin_home_t</strong>:s0 /var/www/html/hello.html
</pre>

the <code>restorecon</code> command essentially sets the security context for a file/folder based on the rules specified by <code>semanage fcontext -l</code> command.  

In the above example, we applied the "restorecon" command to only one file. However if you want to run the restorecon command on every single file on your machine, then you can do this by simply creating the following file empty file:

<pre>
$ touch /.autorelabel
$ ls -la / | grep '.autorelabel'
-rw-r--r--.   1 root    root       0 Oct 13 20:02 .autorelabel
</pre>

After this file has been created, you simply reboot the machine for the autorelabel to start. This can be quite time consuming and as result your machine's reboot may take several minutes longer then it usually does. Once the relabelling is complete, the .autorelabel file is automatically deleted. 

Note, creating this /.autorelabel null file is something we need to do when we don't know what the root password is and therefore we perform a password reset. 

An alternative way to doing a complete machine level restorecon is by doing changing the default SELinux mode to disabled and then switching the default back to either permissive of enabled again. This approach will require you do 2 machine reboots instead of one. 


<h2>A common example of why you need to create your own targeted policies</h2>

By default httpd will use the /var/www as the Documetroot. However you might want to change this default to a new custom folder, e.g.:

<pre>
$ mkdir /webcontent
$ echo "hello world" > /webcontent/index.html
$ chown -R apache:apache /webcontent
</pre>

You would then update the httpd config file to point to this new "DocumentRoot" folder.

Now ensure that selinux mode is set to enabled:

<pre>
$ setenforce Enforcing
$ getenforce
Enforcing
</pre>

Now if you open up your servers homepage in a web browser you will find that you get an error message. That's because the security context of the /webcontent folder and it's children is wrong:

<pre>
$ ls -laZ /webcontent
drwxr-xr-x. apache apache unconfined_u:object_r:<strong>default_t</strong>:s0 .
dr-xr-xr-x. root   root   system_u:object_r:root_t:s0      ..
-rw-r--r--. apache apache unconfined_u:object_r:<strong>default_t</strong>:s0 index.html
</pre>

That's because <code>$ semanage fcontext -l</code> doesn't contain any specific policies for our custom made folder:

<pre>
$ semanage fcontext -l | grep webcontent
$
</pre> 


Therefore our folder and it's children just got a context with the generic type "default_t". 

However for the apache process to be able to access /webcontent folder and it's contents, there security context needs to be the same as that of the original default DocumentRoot folder:

<pre>
$ ls -laZ /var | grep www
drwxr-xr-x. root root system_u:object_r:<strong>httpd_sys_content_t</strong>:s0 www
</pre>

By reviewing the examples in the semanage-fcontext man page, we found that we need to run the following command to define our new security context for the /webcontent folder and it's children:

<pre>
$ semanage fcontext -l | grep webcontent
$ semanage fcontext -a -t httpd_sys_content_t "/webcontent(/.*)?"
</pre>

Now let's confirm this policy has been added to the catalog:

<pre>
$ semanage fcontext -l | grep webcontent
/webcontent(/.*)?         all files          system_u:object_r:httpd_sys_content_t:s0
</pre>

Finally we can now apply this policy to our folder and it's children.

First let's check again what it is currently:

<pre>
$ ls -laZ /webcontent
drwxr-xr-x. apache apache unconfined_u:object_r:default_t:s0 .
dr-xr-xr-x. root   root   system_u:object_r:root_t:s0      ..
-rw-r--r--. apache apache unconfined_u:object_r:default_t:s0 index.html
</pre>

Now apply our new policy to the /webcontent folder and it's children:

<pre>
$ restorecon -r /webcontent
</pre>

Now let's check again:

<pre>
$ ls -laZ /webcontent
drwxr-xr-x. apache apache unconfined_u:object_r:<strong>httpd_sys_content_t</strong>:s0 .
dr-xr-xr-x. root   root   system_u:object_r:root_t:s0      ..
-rw-r--r--. apache apache unconfined_u:object_r:<strong>httpd_sys_content_t</strong>:s0 index.html
</pre>

We should now be able to access the index.html via a web browser.