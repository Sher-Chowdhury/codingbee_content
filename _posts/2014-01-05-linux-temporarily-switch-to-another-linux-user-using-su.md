---
ID: 47
post_title: >
  linux-temporarily-switch-to-another-linux-user-using-su
author: admin
post_excerpt: ""
layout: post
permalink: http://localhost:8000/?p=47
published: true
post_date: 2014-01-05 00:00:00
---
## How to switch users or temporarily become root

## Commands covered in this article

id            # Displays the username of the currently logged in user. It also displays what groups the user belongs to. su        # Lets you (s)witch to another (u)ser while logged in as someone else. sudo      # xxxxxxxxxxxxxxxxx

## Become the root user using "su"

In linux it is always recommended that you avoid logging in as the root user directly. That's because it can cause security issues and increases the chances of damaging something. In fact, Centos will display a warning message when you attempt a direct root login.

So the best practice is that you always login as a normal user. However there are lot of commands that can only be run as the root user, and in those instances, the recommended way to run them is by using the (s)witch (u)ser utility, which lets you switch from one user to another:

su - root      # This lets you switch to the root user. I'll cover the "-" argument later. password      # The su command prompts for the password of the user you are switching to. In this case it is the root user's password. id                  # The id command simply displays which user youre session is currently running under.

Note, you can only use the su command if you know the password for the user you are switching to. The only exception to this rule is if you are logged in as the root user, In which case you can su to any user without a password prompt.

login: password: id su -            # If you leave out the username, like here, then by default, su will assume you mean root. password id su - Tom id                  # Notice that we didn't need to enter Tom's password.

## Switching back to the previous user

In the above example we switched from John-=>root-=>Tom. This meant that our current shell is 2-sessions-deep. So if you want switch back to John, then you can use the exit command:

id exit      # This takes you backup one step, which in this case is the root user. id exit      # Now you are back to the John, and you can't exit any further. If you try to exit again, then the terminal itself will close.

Instead of doing the above, you could have simply done:

su - John      # in this case, you would be 3-sessions deep, i.e. John-=>root-=>Tom-=>John

# It is good practice, where possible to never go deeper than 3 levels. Otherwise you might start to experience odd behaviours.

## What does the "-" mean in 'su -'?

The first parameter, "-",   tells su to pretend as if the user logged in directly. Otherwise, the environment is passed   along, with the exception of $PATH, which is controlled by PATH and SUPATH in /etc/default/su.

This "-" basically means start the new session from fresh, and dont carry any 'baggage' from the previous session. For example, if you create a variable, and export it, then this variable is only available if you don't start a fresh session:

testvar=12345      # Here I created a new variable. export testvar                  # This sets the variable to become available in all sessions. su Tom                  # Since "-" is ommitted, carry over is allowed. So variable should be accessible in the new session. password echo testvar        # variable exists in the new session. exit su - Tom            # Since "-" is specified , it blocks any carry over, in order to mimic a close a direct login as possible. echo testvar       # Returns a blank since this variable doesn't exist in a "fresh" session.

## Run a single command as another user.

You can use su to switch user just long enough to run a single command before backing out again:

su - Tom -c 'echo "Hello and Goodbye Tom'

Or you can merge multiple commands in to single line using semi-colon:

su - Tom -c 'id ; echo "Hello and Goodbye Tom'

## Give non-admins access to some root level using sudo

In Linux, all admin level   commands/tasks that can only be performed by the root user. So if there is a particular admin-level activity that you want a colleague to perform, then one way to do this is to tell them the root password. However this is not a good idea because:

1.  of security reasons, it is best practice to limit as far as possible, who has root access.
2.  you are inadvertantly giving them full control of everything else.

A way round this dilemma is to use the sudo utility. Sudo allows the root user to give a user the ability to run one or more admin-level commands (as if they are logged in as root user but without actually logging in as root user).

For example, only root can shutdown the machine:

shutdown      # This shutdowns a machine. To switch it on again, you need to press the power-button on the machine.

If user, Tom tries to use the shutdown command, then he gets a permission denied message:

<code=>

If the root user gives Tom, sudo permission to use the "shutdown" command. Then Tom should now be able to run the admin-level command be preceding it with the word sudo:

sudo {command}

As indicated above, the syntax for initiating sudo is the word sudo following by the command to be run:

Hence Tom can invoke his sudo privelege like this:

shutdown            # this wont work because sudo hasn't been invoked. sudo shutdown      # This worked, and machine shuts down. <password=>            # Like su, you have to enter a password. But unlike su, you actually enter the password of the current user.

# This is a little overkill since user is already logged in as that user. Fortunately this <l-anchor=>sudo password<l=> prompt can be disabled.

For security reasons, a log is kept of all sudo related activities, in case a user abuses their sudo priveleges.

The root user needs to <l=>configure and assign sudo priveleges<l=> in order to assign priveleges.

## The main difference between using su and sudo (for running admin commands)

To summarise, The main difference for running admin-level commands as the main user are:

1.  To use su, you need to know the root password, whereas for sudo, you don't
2.  With sudo, you need to prefix every command with the word "sudo", but no prefixing is necessary with su.
3.  With su, you can run any admin-command, but with sudo, you can only run the admin-commands as authorised in the sudo's config file.

## Some useful facts

The root user is sometimes referred to as the "superuser". An alternative to the "id" comamnd "whoami" (but it isn't as informative). There is a convention that for a root user, the prompt user ends in "#" whereas for anyone else it ends with "$". Speaking of which, there are a few ways to <l=>customise the command prompt<l=>.

The id command shows more than just username and group names. It also shows corresponding id numbers for the user and groups. You can also use the id command to view details of other users aside from the currently logged in user:

id {username}

The id command gets its information from /etc/passwd and /etc/group

## Related topics

Setting up and assigning sudo privileges.

## Useful links

<http://www.cyberciti.biz/open-source/command-line-hacks/linux-run-command-as-different-user/> <http://www.cyberciti.biz/faq/how-can-i-log-in-as-root/> <http://www.cyberciti.biz/tips/allow-a-normal-user-to-run-commands-as-root.html>