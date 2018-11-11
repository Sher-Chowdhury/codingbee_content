---
ID: 105
post_title: 'Puppet &#8211; Resources'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-resources
published: true
post_date: 2014-05-25 00:00:00
---
Resources are the fundamental unit for modelling your system configurations. They are essentially building blocks, like legos. Each resource describe a particular thing, e.g. a service that must be running, or a package that must be installed.

The block of Puppet code that defines a resource is called a <strong>resource declaration.</strong> This block of code is written in a language called the "Declarative Modelling Language" (aka DML). Here is an example of what a resource declaration looks like:

<pre>
user { 'gary':
ensure => present,
uid    => '101',
shell  => '/bin/bash',
home   => '/home/Gary',
}
</pre>
The above is an example of a "resource declaration" for a resource of the type "user". and it is made up of 4 parts:
<ol>
	<li><strong>Resource Type</strong> - which in this case is "<a href="https://docs.puppetlabs.com/references/latest/type.html#user">user</a>"</li>
	<li><strong>Resource Title</strong> - which in this case is "gary"</li>
	<li><strong>Attributes</strong> - these are the resource's properties, e.g. ensure, uid, gid,...etc</li>
	<li><strong>Values</strong> - this are the values that correspond to each porperties</li>
</ol>
Note: Each resource declaration is self contained and you can pick and choose which resources you want as a way to define what a target node should look like. The ordering of the resources doesn't matter, although you can add dependencies by using <a href="https://docs.puppetlabs.com/references/latest/metaparameter.html">metaparameters</a>.

<strong>Resource Types</strong>

There are various types of resources, and the above resource is an example of a "<a href="https://docs.puppetlabs.com/references/latest/type.html#user">user</a>" resource type. There are actually a lot of other types of resources. You can view a list of the available resource types by using puppet's "describe" sub-command along with it's "--list" option:
<pre>[root@puppetmaster ~]# puppet describe --list
These are the types known to puppet:
augeas          - Apply a change or an array of changes to the  ...
computer        - Computer object management using DirectorySer ...
cron            - Installs and manages cron jobs
exec            - Executes external commands
file            - Manages files, including their content, owner ...
filebucket      - A repository for storing and retrieving file  ...
group           - Manage groups
host            - Installs and manages host entries
interface       - This represents a router or switch interface
k5login         - Manage the `.k5login` file for a user
macauthorization - Manage the Mac OS X authorization database
mailalias       - .. no documentation ..
maillist        - Manage email lists
mcx             - MCX object management using DirectoryService  ...
mount           - Manages mounted filesystems, including puttin ...
nagios_command  - The Nagios type command
nagios_contact  - The Nagios type contact
nagios_contactgroup - The Nagios type contactgroup
nagios_host     - The Nagios type host
nagios_hostdependency - The Nagios type hostdependency
nagios_hostescalation - The Nagios type hostescalation
nagios_hostextinfo - The Nagios type hostextinfo
nagios_hostgroup - The Nagios type hostgroup
nagios_service  - The Nagios type service
nagios_servicedependency - The Nagios type servicedependency
nagios_serviceescalation - The Nagios type serviceescalation
nagios_serviceextinfo - The Nagios type serviceextinfo
nagios_servicegroup - The Nagios type servicegroup
nagios_timeperiod - The Nagios type timeperiod
notify          - .. no documentation ..
package         - Manage packages
resources       - This is a metatype that can manage other reso ...
router          - .. no documentation ..
schedule        - Define schedules for Puppet
scheduled_task  - Installs and manages Windows Scheduled Tasks
selboolean      - Manages SELinux booleans on systems with SELi ...
selmodule       - Manages loading and unloading of SELinux poli ...
service         - Manage running services
ssh_authorized_key - Manages SSH authorized keys
sshkey          - Installs and manages ssh host keys
stage           - A resource type for creating new run stages
tidy            - Remove unwanted files based on specific crite ...
user            - Manage users
vlan            - .. no documentation ..
whit            - Whits are internal artifacts of Puppet's curr ...
yumrepo         - The client-side description of a yum reposito ...
zfs             - Manage zfs
zone            - Manages Solaris zones
zpool           - Manage zpools
[root@puppetmaster ~]#
</pre>
Out of this list, the 8 types that are used most often are:

The 8 core resource types that are used most often are:
<ul>
	<li>notify</li>
	<li>file</li>
	<li>package</li>
	<li>cron</li>
	<li>user</li>
	<li>service</li>
	<li>exec</li>
	<li>group</li>
</ul>
<strong>Resource Title</strong>
In the above example, we have named the resource "gary". This is the unique title for this user resource type. You can't have another user-resource of the same name because that would cause a conflict.

You can use the "resource" command to view a list of all the resources that are of the type "user":

[bash]
[root@puppetmaster ~]# puppet resource user
user { 'abrt':
  ensure           =&gt; 'present',
  gid              =&gt; '173',
  home             =&gt; '/etc/abrt',
  password         =&gt; '!!',
  password_max_age =&gt; '-1',
  password_min_age =&gt; '-1',
  shell            =&gt; '/sbin/nologin',
  uid              =&gt; '173',
}
user { 'adm':
  ensure           =&gt; 'present',
  comment          =&gt; 'adm',
  gid              =&gt; '4',
  groups           =&gt; ['sys', 'adm'],
  home             =&gt; '/var/adm',
  password         =&gt; '*',
  password_max_age =&gt; '99999',
  password_min_age =&gt; '0',
  shell            =&gt; '/sbin/nologin',
  uid              =&gt; '3',
}
user { 'apache':
  ensure           =&gt; 'present',
  comment          =&gt; 'Apache',
  gid              =&gt; '48',
  home             =&gt; '/var/www',
  password         =&gt; '!!',
  password_max_age =&gt; '-1',
  password_min_age =&gt; '-1',
  shell            =&gt; '/sbin/nologin',
  uid              =&gt; '48',
}
.
.
.
.
...etc.
 [/bash]

If you want to list the resource of a particular user (e.g. a user resource with the title "apach"), then do:

[bash]
[root@puppetmaster ~]# puppet resource user apache
user { 'apache':
  ensure           =&gt; 'present',
  comment          =&gt; 'Apache',
  gid              =&gt; '48',
  home             =&gt; '/var/www',
  password         =&gt; '!!',
  password_max_age =&gt; '-1',
  password_min_age =&gt; '-1',
  shell            =&gt; '/sbin/nologin',
  uid              =&gt; '48',
}
 [/bash]

<strong>Attributes and Values</strong>
The main body of the resource is made up of attribute-value pairs. Here you can specify values for a given resource's properties.

Each resource type has it's own set of attributes that you can configure. You can use the "describe" subcommand to to find out more about a particular resource-type along with a list of all it's available attributes. For example if you want to view more info about the user-resource-type along with all it's configurable attributes, then do:
<pre>[root@puppetmaster ~]# puppet describe user

user
====
Manage users.   This type is mostly built to manage system
users, so it is lacking some features useful for managing normal
users.
This resource type uses the prescribed native tools for creating
groups and generally uses POSIX APIs for retrieving information
about them.   It does not directly modify `/etc/passwd` or anything.
**Autorequires:** If Puppet is managing the user's primary group (as
provided in the `gid` attribute), the user resource will autorequire
that group. If Puppet is managing any role accounts corresponding to the
user's roles, the user resource will autorequire those role accounts.


Parameters
----------

- **allowdupe**
       Whether to allow duplicate UIDs. Defaults to `false`.
       Valid values are `true`, `false`, `yes`, `no`.

- **attribute_membership**
       Whether specified attribute value pairs should be treated as the
       **complete list** (`inclusive`) or the **minimum list** (`minimum`) of
       attribute/value pairs for the user. Defaults to `minimum`.
       Valid values are `inclusive`, `minimum`.

- **attributes**
       Specify AIX attributes for the user in an array of attribute = value
       pairs.
Requires features manages_aix_lam.

- **auth_membership**
       Whether specified auths should be considered the **complete list**
       (`inclusive`) or the **minimum list** (`minimum`) of auths the user
       has. Defaults to `minimum`.
Valid values are `inclusive`, `minimum`.

- **auths**
       The auths the user has.   Multiple auths should be
       specified as an array.
Requires features manages_solaris_rbac.

- **comment**
       A description of the user.   Generally the user's full name.

- **ensure**
       The basic state that the object should be in.
       Valid values are `present`, `absent`, `role`.

- **expiry**
       The expiry date for this user. Must be provided in
       a zero-padded YYYY-MM-DD format --- e.g. 2010-02-19.
       If you want to make sure the user account does never
       expire, you can pass the special value `absent`.
       Valid values are `absent`. Values can match `/^\d{4}-\d{2}-\d{2}$/`.
       Requires features manages_expiry.

- **forcelocal**
       Forces the mangement of local accounts when accounts are also
       being managed by some other NSS
       Valid values are `true`, `false`, `yes`, `no`.
       Requires features libuser.

- **gid**
       The user's primary group.   Can be specified numerically or by name.
       This attribute is not supported on Windows systems; use the `groups`
       attribute instead. (On Windows, designating a primary group is only
       meaningful for domain accounts, which Puppet does not currently manage.)

- **groups**
       The groups to which the user belongs.   The primary group should
       not be listed, and groups should be identified by name rather than by
       GID.   Multiple groups should be specified as an array.

- **home**
       The home directory of the user.   The directory must be created
       separately and is not currently checked for existence.

- **ia_load_module**
       The name of the I&amp;A module to use to manage this user.
       Requires features manages_aix_lam.

- **iterations**
       This is the number of iterations of a chained computation of the
       password hash (http://en.wikipedia.org/wiki/PBKDF2).   This parameter
       is used in OS X. This field is required for managing passwords on OS X
       &gt;= 10.8.
Requires features manages_password_salt.

- **key_membership**
       Whether specified key/value pairs should be considered the
       **complete list** (`inclusive`) or the **minimum list** (`minimum`) of
       the user's attributes. Defaults to `minimum`.
       Valid values are `inclusive`, `minimum`.

- **keys**
       Specify user attributes in an array of key = value pairs.
       Requires features manages_solaris_rbac.

- **managehome**
       Whether to manage the home directory when managing the user.
       This will create the home directory when `ensure =&gt; present`, and
       delete the home directory when `ensure =&gt; absent`. Defaults to `false`.
       Valid values are `true`, `false`, `yes`, `no`.

- **membership**
       Whether specified groups should be considered the **complete list**
       (`inclusive`) or the **minimum list** (`minimum`) of groups to which
       the user belongs. Defaults to `minimum`.
       Valid values are `inclusive`, `minimum`.

- **name**
       The user name. While naming limitations vary by operating system,
       it is advisable to restrict names to the lowest common denominator,
       which is a maximum of 8 characters beginning with a letter.
       Note that Puppet considers user names to be case-sensitive, regardless
       of the platform's own rules; be sure to always use the same case when
       referring to a given user.

- **password**
       The user's password, in whatever encrypted format the local
       system requires.
       * Most modern Unix-like systems use salted SHA1 password hashes. You can
       use
           Puppet's built-in `sha1` function to generate a hash from a password.
       * Mac OS X 10.5 and 10.6 also use salted SHA1 hashes.
       * Mac OS X 10.7 (Lion) uses salted SHA512 hashes. The Puppet Labs
       [stdlib][]
           module contains a `str2saltedsha512` function which can generate
       password
           hashes for Lion.
       * Mac OS X 10.8 and higher use salted SHA512 PBKDF2 hashes. When
           managing passwords on these systems the salt and iterations properties
           need to be specified as well as the password.
       * Windows passwords can only be managed in cleartext, as there is no
       Windows API
           for setting the password hash.
       [stdlib]: https://github.com/puppetlabs/puppetlabs-stdlib/
       Be sure to enclose any value that includes a dollar sign ($) in single
       quotes (') to avoid accidental variable interpolation.
       Requires features manages_passwords.

- **password_max_age**
       The maximum number of days a password may be used before it must be
       changed.
Requires features manages_password_age.

- **password_min_age**
       The minimum number of days a password must be used before it may be
       changed.
Requires features manages_password_age.

- **profile_membership**
       Whether specified roles should be treated as the **complete list**
       (`inclusive`) or the **minimum list** (`minimum`) of roles
       of which the user is a member. Defaults to `minimum`.
       Valid values are `inclusive`, `minimum`.

- **profiles**
       The profiles the user has.   Multiple profiles should be
       specified as an array.
Requires features manages_solaris_rbac.

- **project**
       The name of the project associated with a user.
       Requires features manages_solaris_rbac.

- **purge_ssh_keys**
       Purge ssh keys authorized for the user
       if they are not managed via ssh_authorized_keys. When true,
       looks for keys in .ssh/authorized_keys in the user's home
       directory. Possible values are true, false, or an array of
       paths to file to search for authorized keys. If a path starts
       with ~ or %h, this token is replaced with the user's home directory.
       Valid values are `true`, `false`.

- **role_membership**
       Whether specified roles should be considered the **complete list**
       (`inclusive`) or the **minimum list** (`minimum`) of roles the user
       has. Defaults to `minimum`.
Valid values are `inclusive`, `minimum`.

- **roles**
       The roles the user has.   Multiple roles should be
       specified as an array.
Requires features manages_solaris_rbac.

- **salt**
       This is the 32 byte salt used to generate the PBKDF2 password used in
       OS X. This field is required for managing passwords on OS X &gt;= 10.8.
       Requires features manages_password_salt.

- **shell**
       The user's login shell.   The shell must exist and be
       executable.
       This attribute cannot be managed on Windows systems.
       Requires features manages_shell.

- **system**
       Whether the user is a system user, according to the OS's criteria;
       on most platforms, a UID less than or equal to 500 indicates a system
       user. Defaults to `false`.
       Valid values are `true`, `false`, `yes`, `no`.

- **uid**
       The user ID; must be specified numerically. If no user ID is
       specified when creating a new user, then one will be chosen
       automatically. This will likely result in the same user having
       different UIDs on different systems, which is not recommended. This is
       especially noteworthy when managing the same user on both Darwin and
       other platforms, since Puppet does UID generation on Darwin, but
       the underlying tools do so on other platforms.
       On Windows, this property is read-only and will return the user's
       security identifier (SID).

Providers
---------
       aix, directoryservice, hpuxuseradd, ldap, pw, user_role_add, useradd,
       windows_adsi
[root@puppetmaster ~]#

</pre>
<strong>Resource Abstraction Layer (RAL)</strong>

The RAL is essentially the name for the following core concepts that fundamentally explains how puppet operates:
<ol>
	<li>Resource - All resource belong to a pre-defined resource type. It's a bit like object-oriented-programming concept where an object is an instance class. However in this case, it is a resource that's an instance of a resource type.</li>
	<li>Abstraction - resources are described independently from the target operating system, i.e. a resource gives enough info to tell you what needs to exist on a puppet agent, regardless of whether the puppet agent is a windows or linux machine. It's then left to puppet to work out how to implement the resource for the given operating, and oyu don't have to worry about how puppet does this behind the scenes</li>
	<li>Layer - It is possible to that you can define an entire machine's setup and configuration in terms of a collection of resources, and you can view/manage all these resource via Puppet's CLI interface Layer.</li>
</ol>
In order for the Abstraction part of the RAL model to work, Puppet separates out the resource from its implementation. The platform-specific-implementation exists in the form of "Providers", you have multiple providers to cover all windows/Linux platforms for each resource-type's attribute. You can use the "describe" subcommand along with it's "--providers" option to view a list of providers for each attribute for a given resource type. For example, to view all the providers for the user resource type, you do:
<pre><code>
</code>[root@puppetmaster ~]# puppet describe user --providers

user
====
Manage users.   This type is mostly built to manage system
users, so it is lacking some features useful for managing normal
users.
This resource type uses the prescribed native tools for creating
groups and generally uses POSIX APIs for retrieving information
about them.   It does not directly modify `/etc/passwd` or anything.
**Autorequires:** If Puppet is managing the user's primary group (as
provided in the `gid` attribute), the user resource will autorequire
that group. If Puppet is managing any role accounts corresponding to the
user's roles, the user resource will autorequire those role accounts.


Parameters
----------

- **allowdupe**
       Whether to allow duplicate UIDs. Defaults to `false`.
       Valid values are `true`, `false`, `yes`, `no`.

- **attribute_membership**
       Whether specified attribute value pairs should be treated as the
       **complete list** (`inclusive`) or the **minimum list** (`minimum`) of
       attribute/value pairs for the user. Defaults to `minimum`.
       Valid values are `inclusive`, `minimum`.

- **attributes**
       Specify AIX attributes for the user in an array of attribute = value
       pairs.
Requires features manages_aix_lam.

- **auth_membership**
       Whether specified auths should be considered the **complete list**
       (`inclusive`) or the **minimum list** (`minimum`) of auths the user
       has. Defaults to `minimum`.
Valid values are `inclusive`, `minimum`.

- **auths**
       The auths the user has.   Multiple auths should be
       specified as an array.
Requires features manages_solaris_rbac.

- **comment**
       A description of the user.   Generally the user's full name.

- **ensure**
       The basic state that the object should be in.
       Valid values are `present`, `absent`, `role`.

- **expiry**
       The expiry date for this user. Must be provided in
       a zero-padded YYYY-MM-DD format --- e.g. 2010-02-19.
       If you want to make sure the user account does never
       expire, you can pass the special value `absent`.
       Valid values are `absent`. Values can match `/^\d{4}-\d{2}-\d{2}$/`.
       Requires features manages_expiry.

- **forcelocal**
       Forces the mangement of local accounts when accounts are also
       being managed by some other NSS
       Valid values are `true`, `false`, `yes`, `no`.
       Requires features libuser.

- **gid**
       The user's primary group.   Can be specified numerically or by name.
       This attribute is not supported on Windows systems; use the `groups`
       attribute instead. (On Windows, designating a primary group is only
       meaningful for domain accounts, which Puppet does not currently manage.)

- **groups**
       The groups to which the user belongs.   The primary group should
       not be listed, and groups should be identified by name rather than by
       GID.   Multiple groups should be specified as an array.

- **home**
       The home directory of the user.   The directory must be created
       separately and is not currently checked for existence.

- **ia_load_module**
       The name of the I&amp;A module to use to manage this user.
       Requires features manages_aix_lam.

- **iterations**
       This is the number of iterations of a chained computation of the
       password hash (http://en.wikipedia.org/wiki/PBKDF2).   This parameter
       is used in OS X. This field is required for managing passwords on OS X
       &gt;= 10.8.
Requires features manages_password_salt.

- **key_membership**
       Whether specified key/value pairs should be considered the
       **complete list** (`inclusive`) or the **minimum list** (`minimum`) of
       the user's attributes. Defaults to `minimum`.
       Valid values are `inclusive`, `minimum`.

- **keys**
       Specify user attributes in an array of key = value pairs.
       Requires features manages_solaris_rbac.

- **managehome**
       Whether to manage the home directory when managing the user.
       This will create the home directory when `ensure =&gt; present`, and
       delete the home directory when `ensure =&gt; absent`. Defaults to `false`.
       Valid values are `true`, `false`, `yes`, `no`.

- **membership**
       Whether specified groups should be considered the **complete list**
       (`inclusive`) or the **minimum list** (`minimum`) of groups to which
       the user belongs. Defaults to `minimum`.
       Valid values are `inclusive`, `minimum`.

- **name**
       The user name. While naming limitations vary by operating system,
       it is advisable to restrict names to the lowest common denominator,
       which is a maximum of 8 characters beginning with a letter.
       Note that Puppet considers user names to be case-sensitive, regardless
       of the platform's own rules; be sure to always use the same case when
       referring to a given user.

- **password**
       The user's password, in whatever encrypted format the local
       system requires.
       * Most modern Unix-like systems use salted SHA1 password hashes. You can
       use
           Puppet's built-in `sha1` function to generate a hash from a password.
       * Mac OS X 10.5 and 10.6 also use salted SHA1 hashes.
       * Mac OS X 10.7 (Lion) uses salted SHA512 hashes. The Puppet Labs
       [stdlib][]
           module contains a `str2saltedsha512` function which can generate
       password
           hashes for Lion.
       * Mac OS X 10.8 and higher use salted SHA512 PBKDF2 hashes. When
           managing passwords on these systems the salt and iterations properties
           need to be specified as well as the password.
       * Windows passwords can only be managed in cleartext, as there is no
       Windows API
           for setting the password hash.
       [stdlib]: https://github.com/puppetlabs/puppetlabs-stdlib/
       Be sure to enclose any value that includes a dollar sign ($) in single
       quotes (') to avoid accidental variable interpolation.
       Requires features manages_passwords.

- **password_max_age**
       The maximum number of days a password may be used before it must be
       changed.
Requires features manages_password_age.

- **password_min_age**
       The minimum number of days a password must be used before it may be
       changed.
Requires features manages_password_age.

- **profile_membership**
       Whether specified roles should be treated as the **complete list**
       (`inclusive`) or the **minimum list** (`minimum`) of roles
       of which the user is a member. Defaults to `minimum`.
       Valid values are `inclusive`, `minimum`.

- **profiles**
       The profiles the user has.   Multiple profiles should be
       specified as an array.
Requires features manages_solaris_rbac.

- **project**
       The name of the project associated with a user.
       Requires features manages_solaris_rbac.

- **purge_ssh_keys**
       Purge ssh keys authorized for the user
       if they are not managed via ssh_authorized_keys. When true,
       looks for keys in .ssh/authorized_keys in the user's home
       directory. Possible values are true, false, or an array of
       paths to file to search for authorized keys. If a path starts
       with ~ or %h, this token is replaced with the user's home directory.
       Valid values are `true`, `false`.

- **role_membership**
       Whether specified roles should be considered the **complete list**
       (`inclusive`) or the **minimum list** (`minimum`) of roles the user
       has. Defaults to `minimum`.
Valid values are `inclusive`, `minimum`.

- **roles**
       The roles the user has.   Multiple roles should be
       specified as an array.
Requires features manages_solaris_rbac.

- **salt**
       This is the 32 byte salt used to generate the PBKDF2 password used in
       OS X. This field is required for managing passwords on OS X &gt;= 10.8.
       Requires features manages_password_salt.

- **shell**
       The user's login shell.   The shell must exist and be
       executable.
       This attribute cannot be managed on Windows systems.
       Requires features manages_shell.

- **system**
       Whether the user is a system user, according to the OS's criteria;
       on most platforms, a UID less than or equal to 500 indicates a system
       user. Defaults to `false`.
       Valid values are `true`, `false`, `yes`, `no`.

- **uid**
       The user ID; must be specified numerically. If no user ID is
       specified when creating a new user, then one will be chosen
       automatically. This will likely result in the same user having
       different UIDs on different systems, which is not recommended. This is
       especially noteworthy when managing the same user on both Darwin and
       other platforms, since Puppet does UID generation on Darwin, but
       the underlying tools do so on other platforms.
       On Windows, this property is read-only and will return the user's
       security identifier (SID).

Providers
---------

- **aix**
       User management for AIX.
       * Required binaries: `/bin/chpasswd`, `/usr/bin/chuser`,
       `/usr/bin/mkuser`, `/usr/sbin/lsgroup`, `/usr/sbin/lsuser`,
       `/usr/sbin/rmuser`.
       * Default for `operatingsystem` == `aix`.
       * Supported features: `manages_aix_lam`, `manages_expiry`,
       `manages_homedir`, `manages_password_age`, `manages_passwords`,
       `manages_shell`.

- **directoryservice**
       User management on OS X.
       * Required binaries: `/usr/bin/dscacheutil`, `/usr/bin/dscl`,
       `/usr/bin/dsimport`, `/usr/bin/plutil`, `/usr/bin/uuidgen`.
       * Default for `operatingsystem` == `darwin`.
       * Supported features: `manages_password_salt`, `manages_passwords`,
       `manages_shell`.

- **hpuxuseradd**
       User management for HP-UX. This provider uses the undocumented `-F`
       switch to HP-UX's special `usermod` binary to work around the fact that
       its standard `usermod` cannot make changes while the user is logged in.
       * Required binaries: `/usr/sam/lbin/useradd.sam`,
       `/usr/sam/lbin/userdel.sam`, `/usr/sam/lbin/usermod.sam`.
       * Default for `operatingsystem` == `hp-ux`.
       * Supported features: `allows_duplicates`, `manages_homedir`,
       `manages_passwords`.

- **ldap**
       User management via LDAP.
       This provider requires that you have valid values for all of the
       LDAP-related settings in `puppet.conf`, including `ldapbase`.   You will
       almost definitely need settings for `ldapuser` and `ldappassword` in
       order
       for your clients to write to LDAP.
       Note that this provider will automatically generate a UID for you if
       you do not specify one, but it is a potentially expensive operation,
       as it iterates across all existing users to pick the appropriate next
       one.
* Supported features: `manages_passwords`, `manages_shell`.

- **pw**
       User management via `pw` on FreeBSD and DragonFly BSD.
       * Required binaries: `pw`.
       * Default for `operatingsystem` == `freebsd, dragonfly`.
       * Supported features: `allows_duplicates`, `manages_expiry`,
       `manages_homedir`, `manages_passwords`, `manages_shell`.

- **user_role_add**
       User and role management on Solaris, via `useradd` and `roleadd`.
       * Required binaries: `passwd`, `roleadd`, `roledel`, `rolemod`,
       `useradd`, `userdel`, `usermod`.
       * Default for `osfamily` == `solaris`.
       * Supported features: `allows_duplicates`, `manages_homedir`,
       `manages_password_age`, `manages_passwords`, `manages_solaris_rbac`.

- **useradd**
       User management via `useradd` and its ilk.   Note that you will need to
       install Ruby's shadow password library (often known as `ruby-libshadow`)
       if you wish to manage user passwords.
       * Required binaries: `chage`, `luseradd`, `useradd`, `userdel`,
       `usermod`.
       * Supported features: `allows_duplicates`, `libuser`, `manages_expiry`,
       `manages_homedir`, `manages_password_age`, `manages_passwords`,
       `manages_shell`, `system_users`.

- **windows_adsi**
       Local user management for Windows.
       * Default for `operatingsystem` == `windows`.
       * Supported features: `manages_homedir`, `manages_passwords`.
[root@puppetmaster ~]#
 
</pre>
You don't need to know much about providers at this stage as they are mainly about the internal workings of puppet.
<h2>Apply a resource to a machine</h2>
After writing a resource, then next thing we would want to do is apply the resource to a machine, so that it's state is changed accordingly.

For the purpose of this demo, we are going to apply the resource locally. We'll cover how to apply the resource to a remote puppet agent later.

&nbsp;

So going back to our previous resource example:

<pre>
user { 'gary':
ensure => present,
uid    => '101',
shell  => '/bin/bash',
home   => '/home/Gary',
}
</pre>

One way to do this, is from the CLI. You can do this by rewriting the resource into a single command, and then passing it into the "resource" subcommand, here's the syntax for this:
<pre>
puppet resource user gary ensure=present uid='101' shell='/bin/bash' home='/home/Gary'
</pre>

Before we do this, let's first let's check that the user "gary" doesn't already exist by grepping the /etc/passwd file:

<pre>
[root@puppetmaster ~]# cat /etc/passwd | grep "gary"
[root@puppetmaster ~]#                                                                
</pre>

Nothing has returned which means that the "gary" user doesn't exist yet. Now let's activate the resource:


<pre>
[root@puppetmaster ~]# puppet resource user gary ensure=present uid='101' shell='/bin/bash' home='/home/Gary'
Notice: /User[gary]/ensure: created
user { 'gary':
   ensure => 'present',
   home     => '/home/Gary',
   shell   => '/bin/bash',
   uid       => '101',
}
[root@puppetmaster ~]#
</pre>

As you can see, puppet outputs the results of running the command, and on this occasion it indicates that the "gary" user now exists. We can confirm this by checking the /etc/passwd file again:

&nbsp;

[bash][root@puppetmaster ~]# cat /etc/passwd | grep &quot;gary&quot;
gary:x:101:501::/home/Gary:/bin/bash
[root@puppetmaster ~]# [/bash]

We can also confirm via puppet using the "resource" sub-command as demonstrated earlier:

[bash] [root@puppetmaster ~]# puppet resource user gary
user { 'gary':
   ensure                     =&gt; 'present',
   gid                           =&gt; '501',
   home                         =&gt; '/home/Gary',
   password                 =&gt; '!!',
   password_max_age =&gt; '99999',
   password_min_age =&gt; '0',
   shell                       =&gt; '/bin/bash',
   uid                           =&gt; '101',
}
[root@puppetmaster ~]#

[/bash]

If you want to undo the changes made by this resource, then you can simply run the above subcommand again, but this time with the absent attribute set to "ensure=absent", i.e.:

<pre>
[root@puppetmaster ~]# <code class="bash comments">puppet resource user gary ensure=present uid='101' shell='/bin/bash' home='/home/Gary'</code>
Notice: /User[gary]/ensure: removed
user { 'gary':
   ensure => 'absent',
}
</pre>


In fact you can just do:

<pre>
[root@puppetmaster ~]# puppet resource user gary ensure=absent
Notice: /User[gary]/ensure: removed
user { 'gary':
   ensure => 'absent',
}
</pre>

Activating resource via the cli as just demonstrated, is actually rarely done in practice. But it can be useful on the odd occasions, e.g. troubleshooting. 

&nbsp;

The proper way to specify a resource is by writing the resource (in the form of a resource declaration) in a file and then instruct puppet to load that file. That's what we'll do in the next chapter.

&nbsp;

info:
See here for <a href="http://docs.puppetlabs.com/learning/ral.html">Resources</a>.