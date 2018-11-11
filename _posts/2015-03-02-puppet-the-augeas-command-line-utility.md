---
ID: 308
post_title: 'Puppet &#8211; The Augeas command line utility'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-the-augeas-command-line-utility
published: true
post_date: 2015-03-02 00:00:00
---
So far we have covered how to ensure the content of a whole file, by using static sample-files which are housed in a puppet module#s "files" folder, or by using .erb files which are stored in the module's "templates" folder. 

However there will be times when you don't want to ensure the content of the whole file, instead you just want to ensure the content of part of a file. For example you may just want to add a line to the /etc/hosts file. 

This is especially the case when dealing with config files that are part of the OS. For example /etc/hosts and /etc/fstab. Other Sys admins may make manual changes to these files. so if you ensure the state of this file using static-files/templates, then it will end up constantly over-riding manual changes made by the system administrators. 

Hence the need to ensure a file's state at a more granular line/section level rather than at a file level. 

Controlling the state of a certain line or (group of lines) is present in a given file is possible in puppet thanks to the <a href="https://docs.puppetlabs.com/references/latest/type.html#augeas">augeas resource type</a>.   


<a href="http://augeas.net/index.html">Augeas</a> is actually a standalone tool, and you can use it completely seperately from Puppet. However Augeas is so powerful and useful, that Puppetlabs decided to develop the <a href="https://docs.puppetlabs.com/references/latest/type.html#augeas">Augeas resource type</a> in order for puppet to natively support it.

<h2>Using Augeas on it's own</h2>
For the time being let's forget about Puppet, and just look at Augeas on it's own. 

To begin with, you first need to install augeas:

<pre>
yum install augeas
</pre> 

The main Augeas standalone tool is a commandline shell, called "augtool":

 

<pre>
[root@puppetmaster /]# rpm -ql augeas
/usr/bin/augparse
/usr/bin/augtool
/usr/bin/fadot
/usr/share/man/man1/augparse.1.gz
/usr/share/man/man1/augtool.1.gz
/usr/share/vim/vimfiles/ftdetect/augeas.vim
/usr/share/vim/vimfiles/syntax/augeas.vim
[root@puppetmaster augeas]# 
</pre>

Hence to start a new augtool, we do:

<pre>
[root@puppetmaster /]# augtool
augtool>
</pre>

You can exit the augtool terminal using the quit command. 


<pre>
[root@puppetmaster /]# augtool
augtool>
</pre>


Use the "help" command to see what other commands are available under augtool:


<pre>
[root@puppetmaster /]# augtool
augtool> help

Admin commands:
  help       - print help
  load       - (re)load files under /files
  quit       - exit the program
  retrieve   - transform tree into text
  save       - save all pending changes
  store      - parse text into tree
  transform  - add a file transform

Read commands:
  dump-xml   - print a subtree as XML
  get        - get the value of a node
  label      - get the label of a node
  ls         - list children of a node
  match      - print matches for a path expression
  print      - print a subtree
  span       - print position in input file corresponding to tree

Write commands:
  clear      - clear the value of a node
  clearm     - clear the value of multiple nodes
  ins        - insert new node
  insert     - insert new node (alias of 'ins')
  mv         - move a subtree
  move       - move a subtree (alias of 'mv')
  rename     - rename a subtree label
  rm         - delete nodes and subtrees
  set        - set the value of a node
  setm       - set the value of multiple nodes
  touch      - create a new node

Path expression commands:
  defnode    - set a variable, possibly creating a new node
  defvar     - set a variable

Type 'help <command>' for more information on a command

augtool> 

</pre>

The augeas tool view files in the form of an object. It can view this object in the form of a hierarchial tree structure. 


Augeas (aka augtool) is used for querying and editing config files from the command line. Augtool lets you navigate/drill-down to a particular part of a config file. 

In our guide, we will use the /etc/hosts file as an example. In our example, the hosts file currently contains:

<pre>
[root@puppetmaster /]# cat /etc/hosts
127.0.0.1 localhost                                            
127.0.1.1 puppetmaster.codingbee.net puppetmaster
10.1.172.10 puppetmaster.codingbee.net puppet puppetmaster
10.1.172.11 puppetagent01.codingbee.net puppetagent01
10.1.172.12 puppetagent02.codingbee.net puppetagent02
10.1.172.13 puppetagent03.codingbee.net puppetagent03
[root@puppetmaster /]# 
</pre>
Note, as you can see above, we have 6 entries. 



However augeas cannot query/edit any config file, but only those that it is has a schema (aka lens) for. There are a set of <a href="http://augeas.net/stock_lenses.html">stock lenses</a> that comes with augeas by default. These lens are stored in:


<pre>
[root@puppetmaster dist]# pwd
/usr/share/augeas/lenses/dist
[root@puppetmaster dist]# ls -l 
total 840
-rw-r--r-- 1 root root  3550 Nov  2  2012 access.aug
-rw-r--r-- 1 root root  1485 Feb 10 10:51 activemq_conf.aug
-rw-r--r-- 1 root root   841 Feb 10 10:51 activemq_xml.aug
-rw-r--r-- 1 root root  2231 Nov  2  2012 aliases.aug
-rw-r--r-- 1 root root  2564 Nov  2  2012 anacron.aug
.
.
.
...etc
[root@puppetmaster dist]# 
</pre>

Let's just take at the hosts.aug lens file:

<pre>
[root@puppetmaster dist]# cat hosts.aug 
(* Parsing /etc/hosts *)

module Hosts =
  autoload xfm

  let sep_tab = Util.del_ws_tab
  let sep_spc = Util.del_ws_spc

  let eol = Util.eol
  let indent = Util.indent

  let comment = Util.comment
  let comment_or_eol = Util.comment_or_eol
  let empty   = [ del /[ \t]*#?[ \t]*\n/ "\n" ]

  let word = /[^# \n\t]+/
  let record = [ seq "host" . indent .
                              [ label "ipaddr" . store  word ] . sep_tab .
                              [ label "canonical" . store word ] .
                              [ label "alias" . sep_spc . store word ]*
                 . comment_or_eol ]

  let lns = ( empty | comment | record ) *

  let xfm = transform lns (incl "/etc/hosts")
[root@puppetmaster dist]# 
</pre>

Don't worry too much about what all this means, but notice that there are 3 labels defined here, "ipaddr", "canonical", and "alias". These labels are used to label different parts of a config file's line/section. 

Now let's take a look at how to query values in the /etc/hosts file:









To do this we use the augtool's "ls" command.

The "ls" command actually let's you navigate to a particular file you want to change, and then lets you drill down further to a particular line of that file:

<pre>
[root@puppetmaster /]# augtool
augtool> ls /
augeas/ = (none)
files/ = (none)
</pre>

Now since we want to query a file, we drill down into files:

<pre>
augtool> ls /files
etc/ = (none)
boot/ = (none)
augtool> 

</pre>


Since the hosts file resides in /etc folder, we now go there:


<pre>

augtool> ls /files/etc/
puppet/ = (none)
hosts/ = (none)      # this is the one we are interested in. 
.
.
.
....etc
augtool> 

</pre>

Notice that the "hosts/" appears as a directory. That's augeas way of telling us that we can drill down further into the file itself. 

Now let's drill down into hosts file:

<pre>
[root@puppetmaster /]# cat /etc/hosts
augtool> ls /files/etc/hosts
1/ = (none)
2/ = (none)
3/ = (none)
4/ = (none)
5/ = (none)
6/ = (none)
augtool> 
</pre>

The 1-6 entries matches up with the lines in the /etc/hosts file that we saw earlier. 

Now we can pick a line to drill down to a particular line, e.g. let's pick line 3:


<pre>
augtool> ls /files/etc/hosts/3
ipaddr = 10.1.172.10
canonical = puppetmaster.codingbee.net
alias[1] = puppet
alias[2] = puppetmaster
augtool> 

</pre>

At this point we have drilled down to a particular node, and hence we now see labels along with the value it houses. We can now use the "get" command to display a particular label's value:


<pre>
augtool> get /files/etc/hosts/3/ipaddr
/files/etc/hosts/3/ipaddr = 10.1.172.10
augtool> 
</pre>

We can now edit this value using the set command, e.g. let's change it from "10.1.172.10" to "8.8.8.8". We do this using the "set" command:

<pre>
augtool> set /files/etc/hosts/3/ipaddr 8.8.8.8
augtool> get /files/etc/hosts/3/ipaddr
/files/etc/hosts/3/ipaddr = 8.8.8.8
augtool> ls /files/etc/hosts/3
ipaddr = 8.8.8.8
canonical = puppetmaster.codingbee.net
alias[1] = puppet
alias[2] = puppetmaster
augtool> 
</pre>

Now that we have checked the change, and are happy with it, we can then save it and exit:


<pre>
augtool> save
Saved 1 file(s)
augtool> quit
[root@puppetmaster /]# 
</pre>


Now if we check the content of the /etc/hosts file:

<pre>
[root@puppetmaster /]# cat /etc/hosts
127.0.0.1 localhost
127.0.1.1 puppetmaster.codingbee.net puppetmaster
8.8.8.8 puppetmaster.codingbee.net puppet puppetmaster         
10.1.172.11 puppetagent01.codingbee.net puppetagent01
10.1.172.12 puppetagent02.codingbee.net puppetagent02
10.1.172.13 puppetagent03.codingbee.net puppetagent03
[root@puppetmaster /]# 
</pre>

As you can see the 3 config item's ip address has now been changed to 8.8.8.8.

In this example, we just edited an existing line. You can also <a href="http://augeas.net/tour.html">append a new line</a>, delete lines, insert lines....etc. 

Another useful command is the print command. This let's you view a file in the form of an object:

<pre>
augtool> print /files/etc/hosts
/files/etc/hosts
/files/etc/hosts/1
/files/etc/hosts/1/ipaddr = "127.0.0.1"
/files/etc/hosts/1/canonical = "localhost\r"
/files/etc/hosts/2
/files/etc/hosts/2/ipaddr = "127.0.1.1"
/files/etc/hosts/2/canonical = "puppetmaster.codingbee.net"
/files/etc/hosts/2/alias = "puppetmaster\r"
/files/etc/hosts/3
/files/etc/hosts/3/ipaddr = "10.1.172.10"
/files/etc/hosts/3/canonical = "puppetmaster.codingbee.net"
/files/etc/hosts/3/alias[1] = "puppet"
/files/etc/hosts/3/alias[2] = "puppetmaster\r"
/files/etc/hosts/4
/files/etc/hosts/4/ipaddr = "10.1.172.11"
/files/etc/hosts/4/canonical = "puppetagent01.codingbee.net"
/files/etc/hosts/4/alias = "puppetagent01\r"
/files/etc/hosts/5
/files/etc/hosts/5/ipaddr = "10.1.172.12"
/files/etc/hosts/5/canonical = "puppetagent02.codingbee.net"
/files/etc/hosts/5/alias = "puppetagent02\r"
/files/etc/hosts/6
/files/etc/hosts/6/ipaddr = "10.1.172.13"
/files/etc/hosts/6/canonical = "puppetagent03.codingbee.net"
/files/etc/hosts/6/alias = "puppetagent03\r"

</pre>