---
ID: 267
post_title: 'Ruby &#8211; Installing RVM'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-installing-rvm
published: true
post_date: 2015-01-16 00:00:00
---
First off you should avoid installing RVM as the root user. Instead, you should install it as a normal user. However at some points while using rvm, you may need to elevate your privilege using sudo (or better yet, rvmsudo). Because of this it is best that you first login as root user, and run "visudo" then add the following line just below the "root" line:

<pre>
$ usermod -a -G wheel {usernam}
</pre>

Note: the "wheel" group is a special group that comes in most Linux distros. All members of the "wheel" group automatically gets root privileges. You might need to restart your bash terminal after this for changes to take affect. 

After that return to your user and continue with the rest of the steps. 

<a href="https://rvm.io/rvm/basics">RVM Basics</a>

Here is the main <a href="https://rvm.io/rvm">RVM</a> documentation. 

http://stackoverflow.com/questions/5758276/how-do-i-install-ruby-gems-when-using-rvm

Info about <a href="http://unix.stackexchange.com/questions/47434/what-is-the-difference-between-curl-and-wget">curl</a>.

http://stackoverflow.com/questions/4604064/rubygems-bundler-and-rvm-confusion

http://curl.haxx.se/docs/manpage.html

http://stackoverflow.com/questions/9394338/how-do-rvm-and-rbenv-actually-work/9422296#9422296

Note it is recommended to <a href="https://rvm.io/rvm/install#installation-explained">install rvm as non-root user</a>. 

<pre>
gpg --keyserver hkp://keys.gnupg.net --recv-keys D39DC0E3
</pre>

The output from this is:


<pre>
[sher@puppetmaster ~]$ gpg --keyserver hkp://keys.gnupg.net --recv-keys D39DC0E3
gpg: requesting key D39DC0E3 from hkp server keys.gnupg.net
gpg: key D39DC0E3: public key "Michal Papis (RVM signing) <mpapis@gmail.com>" imported
gpg: no ultimately trusted keys found
gpg: Total number processed: 1
gpg:               imported: 1  (RSA: 1)
[sher@puppetmaster ~]$
</pre>


Then run:

<pre>
curl -sSL https://get.rvm.io | bash -s stable
</pre>

Note: you might need to install curl via yum before you can run the above command. 

Let's breakdown what this command is doing by first looking at the curl part. In this case, the curl command basically downloads <a href="https://get.rvm.io">this shell script</a>. Which is also available on github as is called the <a href="https://github.com/wayneeseguin/rvm/blob/master/binscripts/rvm-installer">rvm-installer</a>.  

Here's what the curl options means:

L: this stands for (L)ocation 
s: This means run in (s)ilent mode. This is optional and it's best to omit this so that you can see what's going on. 
S: This means (S)how errors. This is optional, but it is best to leave this in so that you can see any errors/warnings. 

If you review the script, you'll find that it requires a positional parameter, one of the values that this parameter can take is "stable", which according to the shell script will install the latest version of rvm. 

Since curl just retrieves the script, you can easily just pipe it to a file. 

However in the above case we have piped it to bash to run it. We also instructed bash (via <a href="http://superuser.com/questions/519882/seeking-to-upgrade-my-bash-magic-help-decipher-this-command-bash-s-stable">the bash -s option</a>) to pass the first parameter as "stable".  


Therefore when you run this, you get:

<pre>
[sher@puppetmaster ~]$ curl -SL https://get.rvm.io | bash -s stable
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 22817  100 22817    0     0  18550      0  0:00:01  0:00:01 --:--:--  293k
Downloading https://github.com/wayneeseguin/rvm/archive/master.tar.gz

Installing RVM to /home/sher/.rvm/
    Adding rvm PATH line to /home/sher/.profile /home/sher/.mkshrc /home/sher/.bashrc /home/sher/.zshrc.
    Adding rvm loading line to /home/sher/.profile /home/sher/.bash_profile /home/sher/.zlogin.
Installation of RVM in /home/sher/.rvm/ is almost complete:

  * To start using RVM you need to run `source /home/sher/.rvm/scripts/rvm`
    in all your open shell windows, in rare cases you need to reopen all shell windows.

# sher,
#
#   Thank you for using RVM!
#   We sincerely hope that RVM helps to make your life easier and more enjoyable!!!
#
# ~Wayne, Michal & team.

In case of problems: http://rvm.io/help and https://twitter.com/rvm_io

  * WARNING: You have '~/.profile' file, you might want to load it,
    to do that add the following line to '/home/sher/.bash_profile':

      source ~/.profile

[sher@puppetmaster ~]$

</pre>

Then follow the advise:

<pre>

[sher@puppetmaster ~]$ source ~/.profile

</pre>

Notice that running the above command resulted in the rvm getting installed under the user's home directory, in a folder called ".rvm":

<pre>
[sher@puppetmaster ~]$ pwd
/home/sher
[sher@puppetmaster ~]$ which rvm
~/.rvm/bin/rvm
[sher@puppetmaster ~]$
</pre>



Now to confirm that rvm is now installed and working, you can now use the rvm command:

<pre>
[sher@puppetmaster ~]$ rvm --version
rvm 1.26.9 (master) by Wayne E. Seguin <wayneeseguin@gmail.com>, Michal Papis <mpapis@gmail.com> [https://rvm.io/]
[sher@puppetmaster ~]$
</pre>

However at this point nothing else has changed i.e. no new gems have been installed (gem list), the gem environment is unchanged (gem environment), and ruby version is unchanged (ruby --version)



You can also access the help info:


<pre>
rvm help
</pre>

Now, rvm may require other packages to be installed (using yum along with rvmsudo). To check this, run <code>rvm requirements</code>:


<pre>
[sher@puppetmaster ~]$ rvm requirements
Checking requirements for centos.
Installing requirements for centos.
Installing required packages: libyaml-devel, autoconf, gcc-c++, readline-devel, zlib-devel, libffi-devel, openssl-devel, automake, libtool, bison, sqlite-devel..sher password required for 'yum install -y libyaml-devel autoconf gcc-c++ readline-devel zlib-devel libffi-devel openssl-devel automake libtool bison sqlite-devel':

</pre>

Here you'll get a password prompt. In which case exit this and run the above yum command (using rvmsudo). after that rerun <code>rvm requirements</code> to confirm everything is in place. 

However at this point, still nothing else has changed i.e. no new gems have been installed (gem list), the gem environment is unchanged (gem environment), and ruby version is unchanged (ruby --version)

<pre>

</pre>

https://rvm.io/rubies/rubygems (do not use sudo)

See also:

http://www.pluralsight.com/courses/building-linux-server-for-ruby-on-rails

https://www.digitalocean.com/community/tutorials/how-to-install-ruby-on-rails-on-centos-6-with-rvm

https://www.digitalocean.com/community/tutorials/how-to-install-ruby-2-1-0-on-centos-6-5-using-rvm