---
ID: 247
post_title: 'Puppet &#8211; Modules (the &#8220;templates&#8221; folder)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-modules-the-templates-folder
published: true
post_date: 2014-12-25 00:00:00
---
Earlier we saw how to make static files (that are stored in the files folder) present in agents.

However in most case you want to have config files present in an agent that has been modified before being placed on the target machine.

This is made possible thanks to <a title="Ruby - The ERB templating system" href="http://codingbee.net/tutorials/ruby/ruby-the-erb-templating-system/">to Ruby's ERB templating system</a>. An ERB file is basically a text file that contains static content which is mixed in witch some ruby code that generates dynamic conent. The ruby code is encased in a bunch of &lt;%...%&gt; and &lt;%=...%&gt; tags which are embeded amongst the static texts. These tags acts as placeholders and contains ruby code that are executed during rendering.

If these tags are supposed to do some processing, but not output any text, then we use &lt;%...%&gt;. However if they are supposed to do some processing, and then output some texts, then we use &lt;%=...%&gt;, this text then replaces the entire ruby code and tags.

Puppet's templating system makes use of the file resource type's "content" attribute. So take a look at the content attribute first. Let's say we have the following in the site.pp file:
<pre>[root@puppetmaster manifests]# pwd
/etc/puppet/manifests
[root@puppetmaster manifests]# ls
site.pp
[root@puppetmaster manifests]# cat site.pp
node 'PuppetAgent1' {
  class {user_account:}
}

node 'PuppetAgent2' {
  class {user_account:
   username =&gt; "bart",
  }
}
[root@puppetmaster manifests]#


</pre>
Now the user_account class resides in the init.pp file of the user_account module:
<pre>[root@puppetmaster modules]# pwd
/etc/puppet/modules
[root@puppetmaster modules]# tree
.
└── user_account
    ├── files
    ├── lib
    ├── manifests
    │     └── init.pp        # this houses the "user_account" class. 
    ├── spec
    └── templates
        └── user-master-copy.erb

6 directories, 2 files
[root@puppetmaster modules]#

</pre>
If we look at the contents of the init.pp, we find:
<pre>class user_account ($username = 'homer'){

  user { $username:
    ensure =&gt; present,
    uid    =&gt; '101',
    shell  =&gt; '/bin/bash',
    home   =&gt; "/home/$username",
  }

  $message = "hello world"
  file { '/tmp/user.txt':
    ensure  =&gt; file,
    content  =&gt; "The message is: $message.\n The 'osfamily' fact's value is: $osfamily. \n",
  }
}
</pre>
As you can see, this class defines the state of 2 resources, first it ensures a particular user account exists (which defaults to username "homer", if site.pp file doesn't specify otherwise by passing through a class parameter). Second it ensures the existence of a file called "/tmp/user.txt" on the agents.

Notice a couple of things here, in the previous post, when we wanted to generate a file resource, using a static file, we used the file resource type's "<a href="https://docs.puppetlabs.com/references/latest/type.html#file-attribute-source">source</a>" attribute, which is designed for copying across static files to the agents. However this time we are using the "<a href="https://docs.puppetlabs.com/references/latest/type.html#file-attribute-content">content</a>" attribute. The "content" attribute is used to populate the file's content with a string, which in the above case, the "string content" is derived from the $message variable as well as a facter variable's value. Whereas, the "source" attribute is limited to simple copy+paste of a static file.
<pre>[root@puppetagent1 tmp]# pwd
/tmp
[root@puppetagent1 tmp]# cat user.txt
cat: user.txt: No such file or directory
[root@puppetagent1 tmp]# puppet agent --test
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppetagent1.codingbee.dyndns.org
Info: Applying configuration version '1419796069'
Notice: /Stage[main]/User_account/File[/tmp/user.txt]/ensure: defined content as '{md5}a80267da88c5ccc0b7e956cf4e2615cc'
Notice: Finished catalog run in 0.07 seconds
[root@puppetagent1 tmp]# cat user.txt
The message is: hello world.
 The 'osfamily' fact's value is: RedHat.
[root@puppetagent1 tmp]#

</pre>
As you can see, with the help of the content attribute, we are already started creating files with some content generated inside it. This approach is suitable if the file is intended to be a really short file.

However if the file is supposed to be bigger, then it's better to use templates.

From above, we have shown you that we have a erb file in the templates folder called "user-master-copy.erb". Let's say this file's content is just "Hello world":
<pre>
/etc/puppet/modules/user_account/templates
[root@puppetmaster templates]# cat user-master-copy.erb
Hello world.

</pre>
Now let's say we want our user_account class file to use this erb file when ensuring the /tmp/user.txt resource, in the case the init.pp file needs to be modified to:
<pre>class user_account ($username = 'homer'){

  user { $username:
    ensure =&gt; present,
    uid    =&gt; '101',
    shell  =&gt; '/bin/bash',
    home   =&gt; "/home/$username",
  }

  file { '/tmp/user.txt':
    ensure  =&gt; file,
    content  =&gt; template("user_account/user-master-copy.erb"),
  }
}

</pre>
Here we now have to make use of the <a href="https://docs.puppetlabs.com/references/latest/function.html#template">template function</a>. All this function does is processes the erb file in order to generate a string. This string is then assigned to the content attribute. Since our erb file didn't contain any ruby code, it meant that the template simply outputs the static content as is, therefore when we do a puppet run, we simply get the user.txt file with "Hello world":
<pre>[root@puppetagent1 tmp]# puppet agent --test
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppetagent1.codingbee.dyndns.org
Info: Applying configuration version '1419797221'
Notice: /Stage[main]/User_account/File[/tmp/user.txt]/ensure: defined content as '{md5}fa093de5fc603823f08524f9801f0546'
Notice: Finished catalog run in 0.07 seconds
[root@puppetagent1 tmp]# cat user.txt
Hello world.
[root@puppetagent1 tmp]# 
</pre>
Now let's place some ruby code in the erb file, and then see what happens. For example we'll use Ruby's "<a href="http://ruby-doc.org/core-2.2.0/Time.html">Time</a>" class, along with it's "now" method to output the time inside the erb file:
<pre>[root@puppetmaster templates]# pwd
/etc/puppet/modules/user_account/templates
[root@puppetmaster templates]# cat user-master-copy.erb
Hello world. The time is now &lt;%= Time.now %&gt;. Have a great day.
[root@puppetmaster templates]#
</pre>
Now if we do a puppet run, we get:
<pre>[root@puppetagent1 tmp]# puppet agent --test
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppetagent1.codingbee.dyndns.org
Info: Applying configuration version '1419798013'
Notice: /Stage[main]/User_account/File[/tmp/user.txt]/ensure: defined content as '{md5}0f04308d1651949f08e8379e4ceb4933'
Notice: Finished catalog run in 0.09 seconds
[root@puppetagent1 tmp]# cat user.txt
Hello world. The time is now Sun Dec 28 20:20:13 +0000 2014. Have a great day.
[root@puppetagent1 tmp]#

</pre>
One of the crucial things you'll want in puppet is the ability to make call/use the following types of data from within your template:
<ul>
	<li>class parameters</li>
	<li>facts</li>
	<li>variables from current scope, or parent scopes.</li>
	<li>variables from another scope:
<ul>
	<li>variable from another class within same module</li>
	<li>varaible from another module</li>
</ul>
</li>
</ul>
&nbsp;
<h3>parameters, facts, and current/parent scope variables</h3>
Let's first look at how to use class parameters, facts, and current/parent scope variables, in erb files.

Let's say we have the following module:

&nbsp;
<pre>
[root@puppetmaster modules]# tree /etc/puppet/modules/
/etc/puppet/modules/
└── user_account
    ├── files
    ├── lib
    ├── manifests
    │     └── init.pp
    ├── spec
    └── templates
        └── user-master-copy.erb

6 directories, 2 files
[root@puppetmaster modules]#

</pre>
&nbsp;

&nbsp;
The site.pp file contains:
&nbsp;
<pre>
[root@puppetmaster modules]# cat /etc/puppet/manifests/site.pp
$siteppvar = "testing 123"

node 'PuppetAgent1' {
  class {user_account:}
}

node 'PuppetAgent2' {
  class {user_account:
    username => "bart",
  }
}
[root@puppetmaster modules]#

</pre>

Note here that we defined a variable called "$siteppvar". We'll call this variable later, within the erb file. 
&nbsp;
And the init.pp file contains:


<pre>
[root@puppetmaster modules]# cat user_account/manifests/init.pp
class user_account ($username = 'homer'){

  user { $username:
    ensure => present,
    uid    => '101',
    shell  => '/bin/bash',
    home   => "/home/$username",
  }

  $greetings = "have a great day!!!"

  file { '/tmp/user.txt':
    ensure  => file,
    content  => template("user_account/user-master-copy.erb"),
  }
}
[root@puppetmaster modules]#

</pre>
&nbsp;
Notice here that we have defined a variable called "$greetings". We are going to call this variable from within the erb file, later. 

Also notice that the file's "content" attribute calls the template function which in turn has the erb file passed into it as an argument. 

&nbsp;
Now here's the content of the erb file:
&nbsp;

<pre>
[root@puppetmaster templates]# cat user-master-copy.erb
Hello <%= @username %>.                        <%# here is a class parameter %>
<%- if @username == "homer" -%>
  Your name is: <%= @username %>
<%- else -%>
  Sorry you are not homer.
<%- end -%>
<%-# Here is some simple ruby code -%>
The time is now <%= Time.now %>.

<%-# Here is a simply ruby for-loop -%>
<%- for fruit in ['apple','banana','mango'] -%>
  A <%= fruit %> is a fruit.
<%- end -%>

<%-# here is a facter -%>
The OS of this machine = <%= @osfamily %>

<%-# here is a variable that is one scope up -%>
Here is a init.pp variable: <%= @greetings %>

<%-# here is a variable that is 2 scope up -%>
Here is a site.pp variable: <%= @siteppvar %>


</pre>

The cool thing about Puppet, is that all class parameters, facter facts, and parent variables, are pre-loaded into the erb file automatically. These data can then be called from within the erb file by prefixing the variable name with an "@", instead of "$". In the world of ruby, the "@" denotes instance variables. 

Therefore, when we do a puppet run on an agent, we get:

<pre>
[root@puppetagent1 ~]# puppet agent --test
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppetagent1.codingbee.dyndns.org
Info: Applying configuration version '1419895997'
Notice: /Stage[main]/User_account/File[/tmp/user.txt]/ensure: defined content as '{md5}b05ac2c2803531f329649b0038b1f4d7'
Notice: Finished catalog run in 0.08 seconds
[root@puppetagent1 ~]# cat /tmp/user.txt
Hello homer.
  Your name is: homer
The time is now Mon Dec 29 23:33:43 +0000 2014.

  A apple is a fruit.
  A banana is a fruit.
  A mango is a fruit.

The OS of this machine = RedHat

Here is a init.pp variable: have a great day!!!

Here is a site.pp variable: testing 123

[root@puppetagent1 ~]#
</pre>

Also just to ensure that the if-statement is working, if we do the puppet run on the 2nd agent, then you get:

<pre>
[root@puppetagent2 ~]# puppet agent --test
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppetagent2.codingbee.dyndns.org
Info: Applying configuration version '1419895997'
Notice: /Stage[main]/User_account/File[/tmp/user.txt]/ensure: defined content as '{md5}0cdb669f843cfe3a0e4c4116c7ba5aa7'
Notice: Finished catalog run in 0.08 seconds
[root@puppetagent2 ~]# cat /tmp/user.txt
Hello bart.
  Sorry you are not homer.
The time is now Mon Dec 29 23:35:00 +0000 2014.

  A apple is a fruit.
  A banana is a fruit.
  A mango is a fruit.

The OS of this machine = RedHat

Here is a init.pp variable: have a great day!!!

Here is a site.pp variable: testing 123

[root@puppetagent2 ~]#
</pre>

<h3>Out of scope variables</h3>

Out of scope variables are variables that don't reside in the current scope or any of the parent scopes. These are therefore usually variables that are defined in the current module but in a different class (i.e. outside the init.pp file) or in a different module altogether. out-of-scope variables are not automatically loaded into the erb files like we saw earlier. 

However puppet does pass into the erb file an object called "scope". And this object contains all out-of-scope variables. You can access this info by using one of the scope object's method, which is called "lookupvar". This method requires one arguement, which is the fqdn of the variable that you're interested in. 

To see this in action, we created a new class (called "another_class") within the existing module and also separate module (called "another_module") too:

<pre>

[root@puppetmaster modules]# tree /etc/puppet/modules/
/etc/puppet/modules/
├── another_module
│     └── manifests
│         └── init.pp
└── user_account
    ├── files
    ├── lib
    ├── manifests
    │     ├── another_class.pp
    │     └── init.pp
    ├── spec
    └── templates
        └── user-master-copy.erb

8 directories, 4 files
[root@puppetmaster modules]#


</pre>

Now the site.pp file shows:

<pre>
[root@puppetmaster manifests]# cat site.pp
node 'PuppetAgent1' {
  class {another_module:}
  class {user_account::another_class:}
  class {user_account:}
}

node 'PuppetAgent2' {
  class {user_account:
    username => "bart",
  }
}
[root@puppetmaster manifests]#

</pre>

In this example, the user_account module contains the call to the erb template. Notice how this class is called after the other class and module. This is important because we need to have this classes and module's data loaded into the "scope" object before we try to retrieve them.

The main class's init file shows:


<pre>
[root@puppetmaster puppet]# cat modules/user_account/manifests/init.pp
class user_account ($username = 'homer'){

  user { $username:
    ensure => present,
    uid    => '101',
    shell  => '/bin/bash',
    home   => "/home/$username",
  }


  file { '/tmp/user.txt':
    ensure  => file,
    content  => template("user_account/user-master-copy.erb"),
  }
}
[root@puppetmaster puppet]#
</pre>
 

The another_class.pp file contains:

<code>

[root@puppetmaster puppet]# cat modules/user_account/manifests/another_class.pp
class user_account::another_class {

  $sidescope = "this variable is from a different class."
}
[root@puppetmaster puppet]#

</code>


Next we have the other module's init.pp file:



<pre>



[root@puppetmaster manifests]# cat init.pp
class another_module {

  $sidescope = "this variable is from a different module."
}
[root@puppetmaster manifests]#

</pre>


Finally our erb file shows:


<pre>

[root@puppetmaster templates]# cat user-master-copy.erb
Here is a variable from another class: <%= scope.lookupvar('user_account::another_class::sidescope') %>

Here is variable from another module: <%= scope.lookupvar('another_module::sidescope') %>
[root@puppetmaster templates]#
</pre>

Notice how we use the lookupvar method to retrieve the variable's value from the scope objecct. We used the variable's fqdn otherwise the lookupvar method will fail due to ambiguities. The fqdn is usually in the following form:

<pre>
{module-name}::{variable-name}   # if the variable is defined in the init file.   

{module-name}::{class-name}::{variable-name}   # if the variable is defined in a seperate file.
                                               # note, you need also include folder names if the
                                               # manifest file is placed in other folders 
                                               # inside the manifest folder.   
</pre>

Now if we do a puppet run, we get:

<pre>
[root@puppetagent1 tmp]# puppet agent --test
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppetagent1.codingbee.dyndns.org
Info: Applying configuration version '1419959486'
Notice: /Stage[main]/User_account/File[/tmp/user.txt]/ensure: defined content as '{md5}24b67407291deaacfe9e6f32dc3f9138'
Notice: Finished catalog run in 0.12 seconds
[root@puppetagent1 tmp]# cat user.txt
Here is a variable from another class: this variable is from a different class.

Here is variable from another module: this variable is from a different module.
[root@puppetagent1 tmp]#
</pre>

Success!