---
ID: 442
post_title: 'Starting a &#8220;login shell&#8221; or &#8220;interactive shell&#8221; using the switch user (su) command in CentOS/RHEL 7'
author: sher
post_excerpt: "Here we're going to talk about what are interactive and login shells. "
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-starting-a-login-shell-or-interactive-shell-using-the-switch-user-su-command
published: true
post_date: 2018-03-27 00:00:00
---
There are 2 types of bash sessions that you can create:

<ul>
	<li><strong>interactive shell</strong> - This is a generic bash session without any user specific customisations. The key thing here is that the <code>~/.bashrc</code> script gets executed behind the scenes every time this interactive shell is initialised</li>
	<li><strong>login shell</strong> - This is a bash session with user specific customisations loaded in. This is the default bash session type when we create an ssh session. The key thing here is that the <code>~/.bash_profile</code> script gets executed behind the scenes every time this login shell is created. This script is what does the user specific customisation. This script also sources the <code>~/.bashrc</code> script. The <code>/etc/profile</code> is also executed as part of a login shell's initialisation</li>.
</ul>

So basically the main difference between interactive and login shell is whether or not both the <code>~/.bash_profile</code> and <code>/etc/profile</code> has been executed. Here's an example of what this file looks like:

<pre>
$ cat /root/.bash_profile
# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
	. ~/.bashrc
fi

# User specific environment and startup programs

PATH=$PATH:$HOME/bin

export PATH
</pre>


Here is a summary of the main files relating to shell types:


<pre>
/etc/profile
       The system-wide initialization file, executed for login shells
~/.bash_profile
       The personal initialization file, executed for login shells
~/.bashrc
       The individual per-interactive-shell startup file
~/.bash_logout
       The individual login shell cleanup file, executed when a login shell exits

</pre> 



All the following let's you switch user, and switch into a login shell:

<ul>
	<li><code>su - {username}</code></li>
	<li><code>su -l {username}</code></li>
	<li><code>su --login {username}</code></li>
</ul>


You can omit {username}, in which case the "su" command will use the default username, which is "root". If you want to su into an interactive shell, you do:


<pre>
$ su {username}
</pre>

Once again, you can omit {username} to imply the root user. In most cases you would log in using login shells. 


<strong>On the job tip</strong>: as part of troubleshooting, you might want to switch to a service account, which are indicated as being nologin account:

<pre>
$ cat /etc/passwd | grep nologin
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:999:997:User for polkitd:/:/sbin/nologin
rpc:x:32:32:Rpcbind Daemon:/var/lib/rpcbind:/sbin/nologin
rpcuser:x:29:29:RPC Service User:/var/lib/nfs:/sbin/nologin
nfsnobody:x:65534:65534:Anonymous NFS User:/var/lib/nfs:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
chrony:x:998:995::/var/lib/chrony:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
mysql:x:27:27:MariaDB Server:/var/lib/mysql:/sbin/nologin
</pre>

But this will fail if you try:


<pre>
$ su - postfix
This account is currently not available.
</pre>

However you can override this by using the (s)hell option:

<pre>
$ su - postfix -s /bin/bash
</pre>

Also see: <a href="http://codingbee.net/tutorials/rhcsa/rhcsa-interactive-shells-and-login-shells">interactive and login shells</a>