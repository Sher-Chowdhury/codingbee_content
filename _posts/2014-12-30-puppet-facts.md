---
ID: 249
post_title: 'Puppet &#8211; Facts'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-facts
published: true
post_date: 2014-12-30 00:00:00
---
Just before an agent requests for a catalog from the master, the agent first compiles a list of information about itself (in the form of key-value pairs). This information is gathered by a tool called "Facter", and each key-value item is referred to as a "fact". You can run the following command on the agent to view the full of facts that the agent sends to the master, as part of it's catalog request. 

<pre>
[root@puppetagent1 ~]# facter
architecture => x86_64
augeasversion => 1.0.0
bios_release_date => 12/01/2006
bios_vendor => innotek GmbH
bios_version => VirtualBox
blockdevice_sda_model => VBOX HARDDISK
blockdevice_sda_size => 22020587520
blockdevice_sda_vendor => ATA
blockdevice_sr0_model => CD-ROM
blockdevice_sr0_size => 1073741312
blockdevice_sr0_vendor => VBOX
blockdevices => sda,sr0
boardmanufacturer => Oracle Corporation
boardproductname => VirtualBox
boardserialnumber => 0
domain => codingbee.dyndns.org
facterversion => 2.1.0
filesystems => ext4,iso9660
fqdn => puppetagent1.codingbee.dyndns.org
hardwareisa => x86_64
hardwaremodel => x86_64
hostname => puppetagent1
id => root
interfaces => eth0,lo
ipaddress => 192.168.1.175
ipaddress_eth0 => 192.168.1.175
ipaddress_lo => 127.0.0.1
is_virtual => true
kernel => Linux
kernelmajversion => 2.6
kernelrelease => 2.6.32-431.23.3.el6.x86_64
kernelversion => 2.6.32
lsbdistcodename => Final
lsbdistdescription => CentOS release 6.5 (Final)
lsbdistid => CentOS
lsbdistrelease => 6.5
lsbmajdistrelease => 6
lsbrelease => :base-4.0-amd64:base-4.0-noarch:core-4.0-amd64:core-4.0-noarch:graphics-4.0-amd64:graphics-4.0-noarch:printing-4.0-amd64:printing-4.0-noarch
macaddress => 08:00:27:95:E3:60
macaddress_eth0 => 08:00:27:95:E3:60
manufacturer => innotek GmbH
memoryfree => 805.86 MB
memoryfree_mb => 805.86
memorysize => 996.14 MB
memorysize_mb => 996.14
mtu_eth0 => 1500
mtu_lo => 16436
netmask => 255.255.255.0
netmask_eth0 => 255.255.255.0
netmask_lo => 255.0.0.0
network_eth0 => 192.168.1.0
network_lo => 127.0.0.0
operatingsystem => CentOS
operatingsystemmajrelease => 6
operatingsystemrelease => 6.5
osfamily => RedHat
partitions => {"sda1"=>{"uuid"=>"d74a4fa8-0883-4873-8db0-b09d91e2ee8d", "size"=>"1024000", "mount"=>"/boot", "filesystem"=>"ext4"}, "sda2"=>{"size"=>"41981952", "filesystem"=>"LVM2_member"}}
path => /usr/lib64/qt-3.3/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin
physicalprocessorcount => 1
processor0 => Intel(R) Core(TM) i7 CPU         920  @ 2.67GHz
processor1 => Intel(R) Core(TM) i7 CPU         920  @ 2.67GHz
processor2 => Intel(R) Core(TM) i7 CPU         920  @ 2.67GHz
processorcount => 3
productname => VirtualBox
ps => ps -ef
puppetversion => 3.6.2
rubysitedir => /usr/lib/ruby/site_ruby/1.8
rubyversion => 1.8.7
selinux => true
selinux_config_mode => enforcing
selinux_config_policy => targeted
selinux_current_mode => enforcing
selinux_enforced => true
selinux_policyversion => 24
serialnumber => 0
sshdsakey => AAAAB3NzaC1kc3MAAACBAK5fYwRM3UtOs8zBCtRTjuHLw56p94X/E0UZBZwFR3q7WH0x5+MNsjfmdCxKvpY/WlIIUcFJzvlfjXm4qDaTYalbzSZJMT266njNbw5WwLJcJ74KdW92ds76pjgmCsjAh+R9YnyKCEE35GsYjGH7whw0gl/rZVrjvWYKQDOmJA2dAAAAFQCoYABgjpv3EkTWgjLIMnxA0GfudQAAAIBM4U6/nerfn6Qvt43FC2iybvwVo8ufixJl5YSEhs92uzsW6jiw68aaZ32q095/gEqYzeF7a2knrOpASgO9xXqStYKg8ExWQVaVGFTR1NwqhZvz0oRSbrN3h3tHgknoKETRAg/imZQ2P6tppAoQZ8wpuLrXUCyhgJGZ04Phv8hinAAAAIBN4xaycuK0mdH/YdcgcLiSn8cjgtiETVzDYa+jF5n8y7oN031I2ZVMsfyu9Qn6uPzrZNbVE4xk8C+N9Yy6zjBqD09PKbptqGhwLBFh4OrC5/4QA8V/8/PmtBvq/sGh2IK8XKZRJUncFdumd+NrbAIUDc/pshaF2DfkEFuwTihdkQ==
sshfp_dsa => SSHFP 2 1 e3fe40f161a0e09a5713e9d642c3e20b82c3b932
SSHFP 2 2 d75eb88e5e7444008737949725f2dd7ba62f47dd4a23fcdf53dc5a14849a96ab
sshfp_rsa => SSHFP 1 1 c4cedb9dff46020f599a7346d9a923336afd8613
SSHFP 1 2 5d2a76840cbfa407a8bb2cad3e5d06cc9c99fbc5b5917c4c366e242495afc4b9
sshrsakey => AAAAB3NzaC1yc2EAAAABIwAAAQEA3dX6XCeJFjb8uX4fdKlry7XRVWXRqA+nsCzKI3+BC89h2aVfWnyO1BHceJAOFJIyFnQxy2PWWX18Qu1KZMQ/VB4uzP3G1ykVIC1miPGriiAzN6mpfJWWb5jEKno9iTEbCqJEahliJuLGJdPca8peReImudWbYod4SyhCeITJ6sb4gKS1L4VA3TIQvQLstAINdjxN8od1nr7g3V4WTGVqelLXkGfP9WzfGmxoTZgSbi8xWpiIrLk3H07PQ5IHLE4Y02y6k9AFH35wRaLwkC9SFdJCYIOSA7D6IKHP8+kiVZdYUb3iuRjIxeZU2Z1EppWa+Fj3oS2/tXGzqmdwgxlseQ==
swapfree => 1.97 GB
swapfree_mb => 2015.99
swapsize => 1.97 GB
swapsize_mb => 2015.99
timezone => GMT
type => Other
uniqueid => a8c0af01
uptime => 6:04 hours
uptime_days => 0
uptime_hours => 6
uptime_seconds => 21865
uuid => BD8B9D85-1BFD-4015-A633-BF71D9A6A741
virtual => virtualbox
[root@puppetagent1 ~]#

</pre>

Note, some of the these facts overlap with the data already available from the "env". However Puppet does not make direct use of env data. Instead it makes use of all facter data, and to puppet, facter data is treated as global variables.    

The agent always provides this list of facts as part of it's catalog request during the start of a puppet run. 

You can find out more about these <a href="https://docs.puppetlabs.com/facter/latest/core_facts.html">facts on the puppetlabs website</a>. 

If you just want to view the value of just one fact, e.g. osfamily, then you simply do:

<pre>
[root@puppetagent1 ~]# facter osfamily
RedHat
</pre>

These facts are then available to the puppet master as normal top level variables, aka a global variable, and the master can call on the given node's facts in the form of a global variable in any of it manifests in order to compile the catalog for the requesting agent.

Facters are referenced in a manifest, in the same way as an ordinary variable, i.e. with a $ prefix. For example let's say we have the following manifest stored on our agent:

<pre>
[root@puppetagent1 /]# cat /tmp/ostype.pp
if ($osfamily == "RedHat"){
  $message = "This machine OS is of the type $osfamily \n"
}
else {
  $message = "This machine is unknown \n"
}

file { "/tmp/machinetype.txt":

  ensure => file,
  content => "$message"

}
[root@puppetagent1 /]#

</pre>

This manifest ensures the state of a single text file called machinetype.txt, where the content of this file is dictated by the fact, osfamily. As a result, if we apply this manifest, we get:

<pre>
[root@puppetagent1 /]# facter osfamily
RedHat
[root@puppetagent1 /]# puppet apply /tmp/ostype.pp
Notice: Compiled catalog for puppetagent1.codingbee.dyndns.org in environment production in 0.07 seconds
Notice: /Stage[main]/Main/File[/tmp/machinetype.txt]/ensure: defined content as '{md5}f59dc5797d5402b1122c28c6da54d073'
Notice: Finished catalog run in 0.04 seconds
[root@puppetagent1 /]# cat /tmp/machinetype.txt
This machine OS is of the type RedHat
[root@puppetagent1 /]#

</pre> 

<h1>
Custom facts</h1>
The facts we have seen so far are the default ones that facter provides out of the box, hence these are known as "core facts".

However you can also add your own custom facts for a given node. There are a few ways to do this. 

<ul>
	<li>Using the "export FACTER_..." syntax.
	<li>via the $LOAD_PATH setting</li>
	<li>FACTERLIB</li>
	<li>Pluginsync (distribute facts through modules)</li>
</ul>



You can manually add your own facts from the command line, using the "export FACTER_{fact's name}" syntax:

<pre>
[root@puppetagent1 facter]# export FACTER_tallest_mountain="Everest"
[root@puppetagent1 facter]# facter tallest_mountain
Everest
[root@puppetagent1 facter]# env | grep "tallest_mountain"
FACTER_tallest_mountain=Everest
[root@puppetagent1 facter]#


</pre>

In this case, you have to use the export command, otherwise this won't work. As a result the fact also become available as a OS environment variable. However environment variable are recognised as facts by default:

<pre>
[root@puppetagent1 facter]# export hello="world"
[root@puppetagent1 facter]# env | grep "hello"
hello=world
[root@puppetagent1 facter]# facter hello

[root@puppetagent1 facter]#

</pre>  

Custom facts created using this approach only persist in the current session. As soon as you restart your bash session, it disappears. As a result this approach to creating custom facts has very little real world applications. 


<h3>
The $LOAD_PATH approach</h3>

In ruby, $LOAD_PATH is the ruby equivalent of Bash's special parameters, although it is an analogy of bash's environment variable, $PATH. However the $LOAD_PATH is not actually an environment variable, instead it is a <a href="http://en.wikibooks.org/wiki/Ruby_Programming/Syntax/Variables_and_Constants#Pre-defined_Variables">pre-defined variable</a>.

The $LOAD_PATH has a synonym which is "$:". This variable is an array of places to search for and load files.  

To view this variable simply do:

<pre>
[root@puppetagent1 ~]# ruby -e 'puts $LOAD_PATH'           # note you have to use single quotes. 
/usr/lib/ruby/site_ruby/1.8
/usr/lib64/ruby/site_ruby/1.8
/usr/lib64/ruby/site_ruby/1.8/x86_64-linux
/usr/lib/ruby/site_ruby
/usr/lib64/ruby/site_ruby
/usr/lib64/site_ruby/1.8
/usr/lib64/site_ruby/1.8/x86_64-linux
/usr/lib64/site_ruby
/usr/lib/ruby/1.8
/usr/lib64/ruby/1.8
/usr/lib64/ruby/1.8/x86_64-linux
.
</pre>

or, from the irb command line do:

<pre>
[root@puppetagent1 ~]# irb
irb(main):001:0> puts $LOAD_PATH
/usr/lib/ruby/site_ruby/1.8
/usr/lib64/ruby/site_ruby/1.8
/usr/lib64/ruby/site_ruby/1.8/x86_64-linux
/usr/lib/ruby/site_ruby
/usr/lib64/ruby/site_ruby
/usr/lib64/site_ruby/1.8
/usr/lib64/site_ruby/1.8/x86_64-linux
/usr/lib64/site_ruby
/usr/lib/ruby/1.8
/usr/lib64/ruby/1.8
/usr/lib64/ruby/1.8/x86_64-linux
.
=> nil
irb(main):002:0>

</pre>

Let's cd into one of these directories, then create a folder called "facter" and create a blank rb file in it:


<pre>

[root@puppetagent1 ~]# cd /usr/lib/ruby/site_ruby/
[root@puppetagent1 site_ruby]# mkdir facter
[root@puppetagent1 site_ruby]# cd facter/
[root@puppetagent1 facter]# ls
[root@puppetagent1 facter]# touch custom_facts.rb


</pre>

In this blank rb file, lets add the following content:

<pre>
[root@puppetagent1 facter]# cat custom_facts.rb
Facter.add('tallest_mountain') do
  setcode "echo Everest"
end
[root@puppetagent1 facter]#
</pre>

As you can see, we don't define a fact by using something straight forward such as <code>{fact's name} = {fact's value}</code>. Instead you have to define the facter in the above style which is made up of 2 parts the <code>Facter.add('fact_name')</code> element and the <code>setcode</code> element.

This syntax may appear overly declarative and complicated. That's because it is common for a facter's value, to take  the output from a shell command/script.  

The way that facter works is that every time it runs, it scans all the folders listed in $LOAD_PATH, and looks for a directory called "facter", which can be located anywhere within the folder's tree structures. If if finds this folder, it then looks for any ruby files in the facter folder, and loads any defined facts into memory. Note, it is best practice to:

<ul>
	<li>define a single fact per ruby file.</li>
	<li>the ruby file is named after the fact's name. So in the above example, I should rename the "custom_facts.rb" to "tallest_mountain.rb", in order to comply with best practice</li>
</ul>

To summarise, the $LOAD_PATH ruby special variable is used by facter to "autoload" facts.

<h3>The FACTERLIB approach</h3>
FACTERLIB works in a similar way to how $LOAD_PATH works, but it is an OS level environment parameter rather than a ruby special variable.     

By default, this environment variable may not be set:

<pre>
[root@puppetagent1 facter]# env | grep "FACTERLIB"
[root@puppetagent1 facter]#

</pre>


To show how this works, let's create a folder called "some_facts" and set up the following structure. 

<pre>
[root@puppetagent1 tmp]# tree /tmp/some_facts/
/tmp/some_facts/
├── river
│     └── longest_river.rb
└── wall
    └── longest_wall.rb

2 directories, 2 files
[root@puppetagent1 tmp]#
</pre>


The longest_river.rb content is:

<pre>
[root@puppetagent1 river]# cat longest_river.rb
Facter.add('longest_river') do
  setcode "echo Nile"
end
</pre>



And the longest_wall.rb file's content is:

<pre>
[root@puppetagent1 wall]# cat longest_wall.rb
Facter.add('longest_wall') do
  setcode "echo 'China Wall'"
end
[root@puppetagent1 wall]#
</pre>

Now if we see if these facts exists:

<pre>
[root@puppetagent1 wall]# facter longest_river

[root@puppetagent1 wall]# facter longest_wall

[root@puppetagent1 wall]#
</pre>

No luck. That's expected since we haven't defined facterlib yet, which we will do now:

<pre>
[root@puppetagent1 /]# export FACTERLIB="/tmp/some_facts"
[root@puppetagent1 /]# env | grep "FACTERLIB"
FACTERLIB=/tmp/some_facts
</pre>

Let's now try again:

<pre>
[root@puppetagent1 /]# facter longest_wall

[root@puppetagent1 /]# facter longest_river

[root@puppetagent1 /]#

</pre>

Still no luck. That's because, facter_lib approach is not as intelligent as $LOAD_PATH, and doesn't recursively look for rb files from a top level directory. Therefore we now try:

<pre>
[root@puppetagent1 /]# export FACTERLIB="/tmp/some_facts/river:/tmp/some_facts/wall"
[root@puppetagent1 /]# env | grep "FACTERLIB"
FACTERLIB=/tmp/some_facts/river:/tmp/some_facts/wall
</pre>


And then see if facter loads the new facts:


<pre>
[root@puppetagent1 /]# facter longest_river
Nile
[root@puppetagent1 /]# facter longest_wall
China Wall
</pre>

Success!

<h2>Distribute facts through modules</h2>

Note, you may need to update the <a href="https://docs.puppetlabs.com/guides/plugins_in_modules.html#enabling-pluginsync">pluginsync</a> setting in puppet.conf on both master and agent. 


One of the main reasons why we may need custom facts, is becuase we have written a module that requires a custom fact in the first place. In this scenario, a good approach would be that if a module requires some custom facts, then bundle those custom facts into the module itself.

I think what would happen here, is that when an agents submit core facter info to the master during a puppet run, then an intermediary step will happen where the master will send the agent a bunch of custom facts rb files for the agent to process and return a bunch of custom facts back to the master. The master will then make use of the core facts along with the custom facts to generate the catalog.

Now let's see how we can package a custom fact into a module. 

Let's say that we want our module (called user_account) to create a file called /tmp/licence.txt. And this file has to contain:

Licence = {licence-info}  

The licence info itself is the output from the following command:

<pre>
[root@puppetmaster manifests]# uname -o
GNU/Linux
</pre>

In which case the content should be:


<pre>y

Licence = GNU/Linux
</pre>

To achieve is, our site.pp file shows:

<pre>
[root@puppetmaster manifests]# cat site.pp
node 'PuppetAgent1' {
  class {user_account:}
}

node 'PuppetAgent2' {
  class {user_account:
    username => "bart",
  }
}
[root@puppetmaster manifests]#
</pre>

And our user_account module looks like:

<pre>
[root@puppetmaster user_account]# tree
.
├── files
├── lib
│     └── facter
│         └── licence.rb
├── manifests
│     └── init.pp
├── spec
└── templates

6 directories, 2 files
[root@puppetmaster user_account]#

</pre>

Notice here that we created a new folder called facter under the lib folder. And inside our facter folder we created our custom-fact rb file, licence.rb. The content of the rb file is:

<pre>
[root@puppetmaster user_account]# cat lib/facter/licence.rb
Facter.add('licence') do
  setcode "uname -o"
end
[root@puppetmaster user_account]#
</pre>

Here we see that we have defined a fact called "licence" and it is equal to the output from the "uname -o" shell command. 

If we now look at the init file, we see:

<pre>
[root@puppetmaster user_account]# cat manifests/init.pp
class user_account ($username = 'homer'){

  user { $username:
    ensure => present,
    uid    => '101',
    shell  => '/bin/bash',
    home   => "/home/$username",
  }


  file { '/tmp/licence.txt':
    ensure  => file,
    content  => "Licence: $licence \n",
  }
}
[root@puppetmaster user_account]#

</pre>

Notice here that "$licence" is used like a an ordinary variable. That's as expected because the custom fact is designed to make the licence fact available like a global variable. Now when we do a puppet run, we get:

<pre>
[root@puppetagent1 tmp]# puppet agent --test
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Loading facts in /var/lib/puppet/lib/facter/licence.rb
Info: Caching catalog for puppetagent1.codingbee.dyndns.org
Info: Applying configuration version '1420033998'
Notice: /Stage[main]/User_account/File[/tmp/licence.txt]/ensure: defined content as '{md5}a1eba342eec9d0cbf768208b3b9b18ea'
Notice: Finished catalog run in 0.04 seconds
[root@puppetagent1 tmp]# cat /tmp/licence.txt
Licence: GNU/Linux
[root@puppetagent1 tmp]#
</pre>

Success!

<strong>See also:</strong>

https://docs.puppetlabs.com/facter/latest/custom_facts.html
https://docs.puppetlabs.com/facter/latest/fact_overview.html



https://docs.puppetlabs.com/puppet/3.7/reference/lang_facts_and_builtin_vars.html

http://www.tutorialspoint.com/ruby/ruby_predefined_variables.htm