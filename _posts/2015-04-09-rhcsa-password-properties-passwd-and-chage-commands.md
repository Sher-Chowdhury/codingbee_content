---
ID: 350
post_title: 'RHCSA &#8211; Password properties (passwd and chage commands)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-password-properties-passwd-and-chage-commands
published: true
post_date: 2015-04-09 00:00:00
---
<h2>Overview of password policies</h2>
By the end of this article you should be able to answer the following questions:


[accordion multiopen="true"]
[toggle title="What's the command to change the 'donald' user's password?"]
$ passwd donald
[/toggle]
[toggle title="What are the 2 commands available to lock the 'donald' user's account?"]
$ passwd -l donald
# or
$ usermod -L donald 
[/toggle]
[toggle title="What's the command to unlock the 'donald' user's account"]
$ passwd -u donald
# or
$ usermod -U donald 
[/toggle]
[toggle title="Which file holds all user passwords stored in hash/encrypted form?"]
/etc/shadow
[/toggle]
[toggle title="What's the command to look up help info for this file?"]
$ man 5 shadow           # first do: 'whatis shadow' to locate this man page
[/toggle]
[toggle title="What command is used for configuring password policies?"]
chage
[/toggle]
[toggle title="What's the command to view user guide for chage?"]
$ man chage
[/toggle]
[toggle title="What's the command to view the password settings for the user 'donald'?"]
$ chage -l donald
[/toggle]
[toggle title="Which file stores password default password policy settings?"]
/etc/login.defs
[/toggle]
[toggle title="What is the command to prevent 'donald' from using the same password for more than 90 days?"]
$ chage -M 90 donald
[/toggle]
[toggle title="What is the command to warn the user donald to change 5 days before the password expires?"]
$ chage -W 5 donald
[/toggle]
[toggle title="What is the command to expire donald's account on 2016-05-25?"]
$ chage -E 2016-05-25 donald
[/toggle]
[toggle title="What's the command to view what the date will be 100 days from now?"]
$ date -d "+100days" +%F        # outputs date in format: YYYY-MM-DD
[/toggle]
[toggle title="What is the command to lock donald's account if donald doesn't change his password 10 days after it has expired?"]
$ chage -I 10 donald
[/toggle]
[/accordion]




<hr />

There are lots of settings associated to a user account's password. The <code>passwd</code> and <code>chage</code> are the 2 main commands for viewing/managing password. let's first take a look at the passwd command:
<pre>$ passwd --help
Usage: passwd [OPTION...] 
  -k, --keep-tokens   keep non-expired authentication tokens
  -d, --delete        delete the password for the named account (root only)
  -l, --lock          lock the password for the named account (root only)
  -u, --unlock        unlock the password for the named account (root only)
  -e, --expire        expire the password for the named account (root only)
  -f, --force         force operation
  -x, --maximum=DAYS  maximum password lifetime (root only)
  -n, --minimum=DAYS  minium password lifetime (root only)
  -w, --warning=DAYS  number of days warning users receives before password expiration (root
                      only)
  -i, --inactive=DAYS number of days after password expiration when an account becomes
                      disabled (root only)
  -S, --status        report password status on the named account (root only)
  --stdin             read new tokens from stdin (root only)

Help options:
  -?, --help          Show this help message
  --usage             Display brief usage message
</pre>
Here's some examples of how this command is used:
<pre>$ passwd {username}      # This changes the password for a given user 

$ passwd -l {username}     # this (l)ocks a user’s account 

$ passwd -u {username}     # this (u)nlocks a user’s account 
</pre>
Also you can lock/unlock as user's account using the <a href="http://codingbee.net/tutorials/rhcsa/rhcsa-adding-managing-and-removing-users/">usermod</a> command.
<h2>Configuring password policies</h2>
Most of the password policies are configured using the chage command:
<pre>$ chage --help
Usage: chage [options] LOGIN

Options:
  -d, --lastday LAST_DAY        set date of last password change to LAST_DAY
  -E, --expiredate EXPIRE_DATE  set account expiration date to EXPIRE_DATE
  -h, --help                    display this help message and exit
  -I, --inactive INACTIVE       set password inactive after expiration
                                to INACTIVE
  -l, --list                    show account aging information
  -m, --mindays MIN_DAYS        set minimum number of days before password
                                change to MIN_DAYS
  -M, --maxdays MAX_DAYS        set maximim number of days before password
                                change to MAX_DAYS
  -R, --root CHROOT_DIR         directory to chroot into
  -W, --warndays WARN_DAYS      set expiration warning days to WARN_DAYS

</pre>
The chage command essentially makes changes to the <code>/etc/shadow</code> file. So to learn more about the above options, you should check out the shadow's man page:
<pre>$ man 5 shadow  

# also checkout:

$ man chage
</pre>
Bot of these locations have really useful info.

Based on the above info, to view a a user's password policies, for example for a user accound called "donald" we do:
<pre>$ chage -l donald
Last password change                                    : Oct 24, 2015
Password expires                                        : never
Password inactive                                       : never
Account expires                                         : never
Minimum number of days between password change          : 0
Maximum number of days between password change          : 99999
Number of days of warning before password expires       : 7
</pre>
Here you'll see some default values e.g. "99999". These default values can be changed by editing the <code>/etc/login.defs</code> file.

By default, a user account doesn’t expire. However you can set it to expire after a specific period of time using the chage command. For example if you want donald's user account's password to expire after a (M)ax age of 90 days, then you run:
<pre>$ chage -M 90 donald
</pre>
This will mean that the user, donald, cannot use the same password for more than 90 days, and he will need to change his password in less then 90 days.

Now let's check that this command has worked:
<pre>$ chage -l donald
Last password change                                    : Oct 24, 2015
Password expires                                        : Jan 22, 2016
Password inactive                                       : never
Account expires                                         : never
Minimum number of days between password change          : 0
<strong>Maximum number of days between password change          : 90</strong>
Number of days of warning before password expires       : 7
</pre>
If you want give the donald user 5 days warning before the passoword max_day_age is reached, then run:
<pre>$ chage -W 5 donald
</pre>
Now let's check that this has worked:
<pre>$ chage -l donald
Last password change                                    : Oct 24, 2015
Password expires                                        : Jan 22, 2016
Password inactive                                       : never
Account expires                                         : May 25, 2016
Minimum number of days between password change          : 0
Maximum number of days between password change          : 90
<strong>Number of days of warning before password expires       : 5</strong>
</pre>
Straight after a user has changed his password, you might want prevent the user changing the password again straightaway, and instead let a few days pass before doing this. E.g. to prevent donald from change the password within 3 days of the last password change, you do:
<pre>$ chage -m 3 donald
</pre>
Let's check that this has worked:
<pre>$ chage -l donald
Last password change                                    : Oct 24, 2015
Password expires                                        : Jan 22, 2016
Password inactive                                       : never
Account expires                                         : May 25, 2016
<strong>Minimum number of days between password change          : 3</strong>
Maximum number of days between password change          : 90
Number of days of warning before password expires       : 5
</pre>
If you want to set an expiration date of "2016-05-25" for the donald user's account, then run:
<pre>$ chage -E 2016-05-25 donald
</pre>
This is useful for temporary employees who finishes their contract at a fixed date in the future. Let's check this has worked:
<pre>$ chage -l donald
Last password change                                    : Oct 24, 2015
Password expires                                        : Jan 22, 2016
Password inactive                                       : never
<strong>Account expires                                         : May 25, 2016</strong>
Minimum number of days between password change          : 0
Maximum number of days between password change          : 90
Number of days of warning before password expires       : 7
</pre>
The account doesn't actually get deleted. It just means that the account's password expires which effectively means that the user get's locked out of his account.

However let's say you want to set the Expiry date on 100 days from today. This can be a little tricky to figure out because you can only specify a date with the "-E" option. Luckily you can use the <code>date</code> command to work out what the date will be 100 days from now, like this:
<pre>$ date -d "+100days" +%F
2015-11-17
</pre>
If donald no longer plans to use the machine, and in fact forget's that the machine exists in the first place, then you want a way to lock their account after a period of inactivity. The most logical way to determining this is the period of time that has passed after an account's password's --maxdays age has been reached. This period is referred to as the "inactive" period. If you want to set the inactive period for 10 days for the donald user, you do:
<pre>$ chage -I 10 donald
</pre>
Now let's check that this has worked:
<pre>$ chage -l donald
Last password change                                    : Oct 24, 2015
Password expires                                        : Jan 22, 2016
<strong>Password inactive                                       : Feb 01, 2016</strong>
Account expires                                         : May 25, 2016
Minimum number of days between password change          : 3
Maximum number of days between password change          : 90
Number of days of warning before password expires       : 5
</pre>
This means that as soon as the donald user's password reaches 90 days of age, then donald has 10 days left log into the machine (he will automatically be prompted to change password at login time), if they still don't login during those 10 days then the account becomes locked.

<h2>The difference between account expiration and password expiration</h2>
There is a difference between <strong>account expiration</strong> and <strong>password expiration</strong>. When you set "--maxdays" you are effectively setting a password exipiration date. However the user can change their password at any point between the "--minday" and "--inactive" periods to keep their account active. But if the account passes the "Account expiration" date (which is set by the -E option) or the Inactive period, then the account becomes locked and only the root user can unlock the account again.