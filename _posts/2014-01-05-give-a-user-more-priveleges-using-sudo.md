---
ID: 53
post_title: Give a user more priveleges using sudo
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/give-a-user-more-priveleges-using-sudo
published: true
post_date: 2014-01-05 00:00:00
---
## Setting up and assign sudo priveleges

The Sudo utility lets you give a users custom privileges so that they can run commands that they are not normally allowed to run.

For example, only the root user can run the "Shutdown" command. But if you want give another user permission to run this command, then you can do this using sudo.

To configure privileges, all you have to do is edit the sudo's config file (which is called sudoers):

/etc/sudoers

## Use visudo instead of vim.
Be careful that you don't make any errors when editing the sudeors file. Thats becuase any errors can stop your machine from booting up. One way to avoid making errors is to use visudo to edit the sudoers file. visudo is a specialized version of vim that has been specifically designed for editing the sudoers file. visudo has builtin error-checking that will try to warn you if you make any errors.

There are a lot of different sudo configurations that are available. So lets start with the most basic one.

## Grant sudo permission for a regular user
To grant permissions, You simply add a line in the following syntax:

{username} localhost={/path/to/command}

Therefore there are 2 things we need to know in order to   assign sudo priveleges:

1. Who is to receive the new privilege
2. The command which the user will now be able to run. Note: you need to specify the full pathname of the command, and not just the command itself.

Lets assume we have a regular user called Tom and we want to let him run the "shutdown" command. First we need to find the shutdown command's full path (which we can do using the "which" command):

&lt;which shutdown=>

Now we add the following entry in the /etc/sudoers file:

Tom localhost=/usr/sbin/shutdown

If you you want Tom to also have sudo permission for the "reboot" command, then do:

Tom localhost=/usr/sbin/shutdown, /usr/sbin/reboot

If you want to give Tom sudo permission for all command stored in /usr/sbin, then do:

Tom localhost=/usr/sbin/*








## useful links

http://www.thegeekstuff.com/2010/09/sudo-command-examples/
http://cavepopo.hd.free.fr/wordpress/linux/sudo-command-sudoers-file-concepts-and-practical-examples/
http://www.garron.me/en/linux/visudo-command-sudoers-file-sudo-default-editor.html


http://www.thegeekstuff.com/2010/08/bash-shell-builtin-commands/
http://linux.die.net/man/5/sudoers