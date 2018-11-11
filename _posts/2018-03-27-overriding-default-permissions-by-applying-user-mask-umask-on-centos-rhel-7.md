---
ID: 443
post_title: >
  Overriding default permissions by
  applying user mask (umask) on
  CentOS/RHEL 7
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/overriding-default-permissions-by-applying-user-mask-umask-on-centos-rhel-7
published: true
post_date: 2018-03-27 00:00:00
---
In linux, the default permissions for any newly created folder is 777 (rwxrwxrwx), and for any newly created file it is 666 (rw-rw-rw-).However when you actually come to create a file or folder, you'll find that in practice that is not really the case:


<pre>
$ touch testfile.txt
$ mkdir testfolder
$ ls -l testfile.txt
-rw-rw-r--. 1 vagrant vagrant 0 Jul 25 14:31 testfile.txt    # here it is 664
$ ls-l | grep testfolder
drwxrwxr-x. 2 vagrant vagrant 6 Jul 25 14:31 testfolder      # here it is 775

</pre>


The reason for this descrepency is that the default permissions are restricted (i.e. masked) by something called a umask value, which you can view by running the the umask command:


<pre>
$ umask
0002
</pre>

This essentially works by being subtracted from the defaults, i.e. 666-002=664 and 777-002=775. Hence the umask value effectively adjusts the default values to arrive at new default values that are more restrictive. We can update the user mask value like this:


<pre>
$ umask 0022
$ umask
0022
$ touch testfile1.txt
$ mkdir testfolder1
$ ls -l testfile1.txt
-rw-r--r--. 1 vagrant vagrant 0 Jul 25 14:53 testfile1.txt        # now it is 644
$ ls -l | grep testfolder1
drwxr-xr-x. 2 vagrant vagrant 6 Jul 25 14:53 testfolder1          # now it is 755

</pre>


Also the default umask value is different for a privileged user (e.g. root user) than it is for an ordinary user:


<pre>
[root@puppetmaster ~]# umask
0022
[root@puppetmaster ~]# su - vagrant
Last login: Sat Jul 25 14:57:15 BST 2015 on pts/0
[vagrant@puppetmaster ~]$ umask
0002
</pre>

These default umask values are specified in the <code>/etc/profile</code> file:

<pre>
$ cat /etc/profile
# /etc/profile

# System wide environment and startup programs, for login setup
# Functions and aliases go in /etc/bashrc

# It's NOT a good idea to change this file unless you know what you
# are doing. It's much better to create a custom.sh shell script in
# /etc/profile.d/ to make custom changes to your environment, as this
# will prevent the need for merging in future updates.

pathmunge () {
    case ":${PATH}:" in
        *:"$1":*)
            ;;
        *)
            if [ "$2" = "after" ] ; then
                PATH=$PATH:$1
            else
                PATH=$1:$PATH
            fi
    esac
}


if [ -x /usr/bin/id ]; then
    if [ -z "$EUID" ]; then
        # ksh workaround
        EUID=`id -u`
        UID=`id -ru`
    fi
    USER="`id -un`"
    LOGNAME=$USER
    MAIL="/var/spool/mail/$USER"
fi

# Path manipulation
if [ "$EUID" = "0" ]; then
    pathmunge /usr/sbin
    pathmunge /usr/local/sbin
else
    pathmunge /usr/local/sbin after
    pathmunge /usr/sbin after
fi

HOSTNAME=`/usr/bin/hostname 2>/dev/null`
HISTSIZE=1000
if [ "$HISTCONTROL" = "ignorespace" ] ; then
    export HISTCONTROL=ignoreboth
else
    export HISTCONTROL=ignoredups
fi

export PATH USER LOGNAME MAIL HOSTNAME HISTSIZE HISTCONTROL

# By default, we want umask to get set. This sets it for login shell
# Current threshold for system reserved uid/gids is 200
# You could check uidgid reservation validity in
# /usr/share/doc/setup-*/uidgid file
<strong>
if [ $UID -gt 199 ] && [ "`id -gn`" = "`id -un`" ]; then
    umask 002
else
    umask 022
fi
</strong>

for i in /etc/profile.d/*.sh ; do
    if [ -r "$i" ]; then
        if [ "${-#*i}" != "$-" ]; then
            . "$i"
        else
            . "$i" >/dev/null
        fi
    fi
done

unset i
unset -f pathmunge


</pre>


Note: <code>id -gn</code> outputs the group name, and <code>id -un</code> outputs the username. For non-root privileged user, their primary group tends to be the "wheel" group.  

However changing the umask value using the umask command isn't persistant, i.e. it reverts back to the original value when you close your terminal and reopen it again, that's because the <code>/etc/profile</code> script reset's the umask value.  

Hence to make it persistant you simply have to insert your umask command into:


<pre>
$ vim ~/.bashrc
</pre>

Note: the above works for both the <a href="http://codingbee.net/tutorials/rhcsa/rhcsa-interactive-shells-and-login-shells">login and interactive shells</a>. The above makes it persistant for a given user. If however you want to make it persistant machine wide, then you need to edit both the <code>/etc/bashrc</code> and <code>/etc/profile</code> files. Both of these files contains the same if-else statement, as highlighted above.