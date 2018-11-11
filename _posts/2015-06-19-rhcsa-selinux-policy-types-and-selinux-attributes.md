---
ID: 425
post_title: 'SELinux &#8211; Policy Types and Attributes'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-selinux-policy-types-and-selinux-attributes
published: true
post_date: 2015-06-19 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="Which file lists the 3 main policy types and what they mean?"]
/etc/selinux/config
[/toggle]
[toggle title="What are the 3 policy types which one is most commonly used?"]
- targeted: this policy type is by far the most commonly used worldwide.
- minimum: this is a stripped down version of the targeted policy. It is mainly used for low spec machines that doesn’t have enough power (e.g. cpu speed) to cope with targeted SELinux.
- mls: this is a much more beefed up version of targeted and is sometimes used by governments.
[/toggle]
[toggle title="Which folder stores the policy types in the form of a file?"]
/etc/selinux/
# however by mls and minimum isn't installed by default.
[/toggle]
[toggle title="What is the command to locate the other 2 policy types rpm package, so that we can install them?"]
$ yum list available | grep po-typelicy | grep -E 'mls|minimum'
[/toggle]
[toggle title="Which security attributes does the targetted policy type mainly relies on in order for it to function?"]
the "type" security attribute
[/toggle]
[toggle title="Because of this, what is this type of enforcement also known as?"]
Type Enforcement Policy 
[/toggle]
[toggle title="If a bunch of objects has the same value for it's type security value, then these objects are referred to as belonging to the same....?"]
domain
[/toggle]
[toggle title="What is the syntax for a security label?"]
User_u:Role_r:Type_t:Level_s
[/toggle]
[toggle title="What is the command to list all available 'type' security attribute values (ther are a few thousands of these)?"]
$ seinfo <span style="letter-spacing:1.5px">-</span>-type
[/toggle]
[toggle title="What are the command to list all available 'user', role, and level security attributes, respectively?"]
$ seinfo <span style="letter-spacing:1.5px">-</span>-user
$ seinfo <span style="letter-spacing:1.5px">-</span>-role
$ seinfo <span style="letter-spacing:1.5px">-</span>-sensitivity
[/toggle]
[toggle title="If a policy type doesn't define what 'type' security attribute a certain object should have, then what default type security attribute value would it end up getting?"]
unconfined_t
[/toggle]
[toggle title="What are all user account's type security attribute value equal to?"]
unconfined_t    # this includes the root user, 
                # but excludes service accounts.
[/toggle]
[toggle title="Why is this the case?"]
Because that's how the targeted policy defined them as.
[/toggle]
[toggle title="What does unconfined_t mean in terms of access level?"]
These objects (e.g. users) have unrestricted access, i.e. they are not restricted by the SELinux (if SELinux is using the targeted policy). Instead they are only restricted by DAC layers. 
[/toggle]
[toggle title="What are the type security attribute's value for any processes created by a user?"]
unconfined_t    # that's because the inherit this value from the user. 
[/toggle]
[/accordion]

<hr/>



Earlier we mentioned that an SELinux policy is a really big text book that contains policy rules. In RHEL, there are actually 3 text books that are available to choose from, as indicated here:


<pre>
$ cat /etc/selinux/config

# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=permissive
# SELINUXTYPE= can take one of these two values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
<strong>SELINUXTYPE=targeted</strong>

</pre>

here's a brief overview of each of them:



<ul>
	<li><strong>targeted</strong> - this policy type is by far the most commonly used worldwide.</li>
	<li><strong>minimum</strong> - this is a stripped down version of the targeted policy. It is mainly used for low spec machines that doesn't have enough power (e.g. cpu speed) to cope with targeted SELinux.</li>
	<li><strong>mls</strong> - this is a much more beefed up version of targeted and is sometimes used by governments.</li>
</ul>


All your policy types are contained in the following directory:


<pre>
$ ls -l /etc/selinux/
total 12
-rw-r--r--. 1 root root  548 Jun 19 19:43 config
-rw-r--r--. 1 root root 2321 Jun  9  2014 semanage.conf
<strong>drwxr-xr-x. 6 root root 4096 Jun 19 19:44 targeted</strong>
</pre>

As you can see, we only have the targeted-policy installed at the moment. To install the other ones, you do:


<pre>
$ yum list available | grep policy | grep -E 'mls|minimum'
selinux-policy-minimum.noarch     3.13.1-23.el7_1.7    updates
selinux-policy-mls.noarch         3.13.1-23.el7_1.7    updates

$ yum install selinux-policy-minimum selinux-policy-mls -y
.
.
.

$ ls -l /etc/selinux/
total 20
-rw-r--r--. 1 root root  548 Jun 19 19:43 config
<strong>drwxr-xr-x. 6 root root 4096 Jun 20 07:31 minimum</strong>
<strong>drwxr-xr-x. 7 root root 4096 Jun 20 07:30 mls</strong>
-rw-r--r--. 1 root root 2321 Jun  9  2014 semanage.conf
drwxr-xr-x. 6 root root 4096 Jun 19 19:44 targeted



</pre>


Each of these folders contains modules that are dynamically loaded into the kernel as and when the kernel needs them. As well as dynamically unloading them too.  


The main difference between each policy type is which particular security attribute type it uses as the decider for determining access.  


<h2>The targeted policy type</h2>

The targeted policy is by far the most commonly used type that is in use today. That's why it comes preinstalled by default, whereas the others require manually installing them first before you can use them.

The targeted policy primarily makes use of the "type" security attribute, This attribute is used as the main deciding facter on whether one object (e.g. process) can access another (e.g. file).


This technique of using the "targeted policy" security attribute as the main deciding facter is often referred to as the "<strong>Type Enforcement Policy</strong>", or TE policy for short.       


<h2>Process "domains"</h2>
When we talk about processes, we sometimes refer to the type security attribute as a "domain", this is a way that allows us to refer to a group of processes. 

For example:



<pre>
<span style="font-size:10px">$ ps -efZ | grep crond
system_u:system_r:crond_t:s0-s0:c0.c1023 root 662  1  0 07:20 ?        00:00:00 /usr/sbin/crond -n
system_u:system_r:crond_t:s0-s0:c0.c1023 root 671  1  0 07:20 ?        00:00:00 /usr/sbin/atd -f
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 root 2672 1663  0 08:01 pts/0 00:00:00 grep --color=auto crond</span>
</pre>


Here we say both crond and atd processes are running under the cron_t domain. 

This means that the crond process can interact with the atd process if it wants' to. But it can't interact with processes of other domains, such as the following:



<pre>
<span style="font-size:10px">$ ps -efZ | grep -E 'lvm_t|httpd_t|sshd_t' | grep -v grep
system_u:system_r:lvm_t:s0      root       502     1  0 07:20 ?        00:00:00 /usr/sbin/lvmetad -f
system_u:system_r:httpd_t:s0    root      1304     1  0 07:20 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:sshd_t:s0-s0:c0.c1023 root 1321  1  0 07:20 ?        00:00:00 /usr/sbin/sshd -D
system_u:system_r:httpd_t:s0    apache    1407  1304  0 07:20 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0    apache    1408  1304  0 07:20 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0    apache    1409  1304  0 07:20 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0    apache    1410  1304  0 07:20 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0    apache    1411  1304  0 07:20 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND</span>
</pre>









<h2>SELinux Attributes</h2>

Everything that exists on a machine, has a security context assigned to it. 

As mentioned earlier, a security context is a colon-delimited string of 4 security attributes, which we will cover in more detail later. There are 4 types of SELinux attributes:


<ul>
	<li>User</li>
	<li>Role</li>
	<li>Type</li>
	<li>Level</li>
</ul>

SELinux contexts are always shown in a colon-delimited format and sequence <code>User_u:Role_r:Type_t:Level_s</code>. The suffix makes it easier to make out why which type each of the security attribute is, in case you forget what the ordering is.  


The "type" security attribute, is by far the most important one, out of the 4 security attributes
That's because the most commonly used policy type, the "targeted" policy, is a Type Enforcement based policy, i.e. it decides whether one object (e.g. process) can access another object (e.g. file) based mainly on their "type" security attribute value. 

For example let's take a look at the security context for the apache process, along with apache related files and folders :


<pre>
<span style="font-size:10px">$ ps -efZ | grep httpd
system_u:system_r:httpd_t:s0    root      1304     1  0 07:20 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0    apache    1407  1304  0 07:20 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0    apache    1408  1304  0 07:20 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0    apache    1409  1304  0 07:20 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0    apache    1410  1304  0 07:20 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
system_u:system_r:httpd_t:s0    apache    1411  1304  0 07:20 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND

$ ls -lZ /etc/httpd/
drwxr-xr-x. root root system_u:object_r:httpd_config_t:s0 conf
drwxr-xr-x. root root system_u:object_r:httpd_config_t:s0 conf.d
drwxr-xr-x. root root system_u:object_r:httpd_config_t:s0 conf.modules.d
lrwxrwxrwx. root root system_u:object_r:httpd_log_t:s0 logs -> ../../var/log/httpd
lrwxrwxrwx. root root system_u:object_r:httpd_modules_t:s0 modules -> ../../usr/lib64/httpd/modules
lrwxrwxrwx. root root system_u:object_r:httpd_config_t:s0 run -> /run/httpd</span>
</pre>

Here the apache processes all have the "httpd_t" security attribute. So in the targeted policy there will be a policy rule for the httpd_t selinux attribute and it will state that objects with the "httpd_t" can access any objects that has the type security attribute of any of the following:

<ul>
	<li>httpd_config_t</li>
	<li>httpd_log_t</li>
	<li>httpd_modules_t</li>
	<li>....etc.</li>
</ul>



This approach means that an http related object is prevented from interacting with a unrelated object, e.g. cron related. As a result that means that if httpd/apache does get compromise, then the damage is only limited to itself, so things like ftp, cron, sshd,...etc doesn't also get compromised. 

 


To get an idea of how many "type" security attribute values exists, you can do:

<pre>
$ whatis seinfo
seinfo (1)           - SELinux policy query tool

$ seinfo -t | head

Types: 4620
   bluetooth_conf_t
   cmirrord_exec_t
   colord_exec_t
   foghorn_exec_t
   jacorb_port_t
   pki_ra_exec_t
   pki_ra_lock_t
   sosreport_t
$ seinfo -t | wc -l
4622
</pre>

<h2>The "unconfined_t" type security attribute</h2>


<pre>
<span style="font-size:10px">$ id
uid=0(root) gid=0(root) groups=0(root) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
$ ps -efZ | grep unconfined_t | head
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 root 1659 1321  0 07:24 ? 00:00:00 sshd: root@pts/0
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 root 1663 1659  0 07:24 pts/0 00:00:00 -bash
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 root 2800 1663  0 08:11 pts/0 00:00:00 ps -efZ
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 root 2801 1663  0 08:11 pts/0 00:00:00 grep --color=auto unconfined_t
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 root 2802 1663  0 08:11 pts/0 00:00:00 head</span>
</pre>



Any objects of this are essentially not restricted by SELinux in any way (for the given policy type that's being used). In other words you can think of SELinux being disabled for these objects. The only restrictions that these objects abide by are the DAC (Discretionary Access Control) layer restrictions and not MAC (Mandatroy Access Control).

Hence if a policy (usually the targeted policy) doesn't have a policy rule written for a particular object, then they automatically get assigned the unconfined_t security attribute.


One particular thing to bear in mind, is that the targeted policy also assign the "unconfined_t" to nearly all user accounts. 
 
 

Any object that doesn't have an selinux policy rules that assigns it a type security value, will automatically get assigned to with the type security attribute value of "unconfined_t" type. Here are a few such objects. Objects that belong to the unconfined_t domain, can access each other, but more importantly access any other object on the entire system! 

Objects belonging to the unconfined_t domain sounds like a huge security loophole, but that's not really the case. That's because SELinux original objective is to secure internet facing processes (because they are vulnerable to attacks), and ensuring they are secure rather than securing all the internal going ons. It also secures. SELinux also secures processes that start up at boot, in case one of these processes is compromised and evil.  

Objects of this domain effectively are not restricted by SELinux and are subject to only Discretionary Access Control (DAC), and not MAC (which is provided by SELinux). 
 

Also all user accounts (e.g. user accounted created by using the "useradd" command) including the root user, belong to the unconfined_t domain, that's because that's how it is defined in the "targeted" policy type. Consequently this means that any processes generated by a user automatically inherits all the user's selinux attributes:


<pre>
$ id
uid=0(root) gid=0(root) groups=0(root) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
$ id -Z
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
$ ps -ef | grep grep
root      5969  1662  0 00:43 pts/0    00:00:00 grep --color=auto grep
</pre>

However unconfined_t is just a name to represent a domain that is completely SELinux disabled. There are actually a lot more totally unconfined domains, and you can view a full list of them using the seinfo command:


<pre>
$ seinfo --help
Usage: seinfo [OPTIONS] [EXPRESSION] [POLICY ...]

Print information about the components of a SELinux policy.

EXPRESSIONS:
  -c[NAME], --class[=NAME]         print object classes
  --sensitivity[=NAME]             print sensitivities
  --category[=NAME]                print categories
  -t[NAME], --type[=NAME]          print types (no aliases or attributes)
  -a[NAME], --attribute[=NAME]     print type attributes
  -r[NAME], --role[=NAME]          print roles
  -u[NAME], --user[=NAME]          print users
  -b[NAME], --bool[=NAME]          print conditional booleans
  --constrain                      print constrain statements
  --initialsid[=NAME]              print initial SIDs
  --fs_use[=TYPE]                  print fs_use statements
  --genfscon[=TYPE]                print genfscon statements
  --netifcon[=NAME]                print netif contexts
  --nodecon[=ADDR]                 print node contexts
  --permissive                     print permissive types
  --polcap                         print policy capabilities
  --portcon[=PORT]                 print port contexts
  --protocol=PROTO                 specify a protocol for portcons
  --all                            print all of the above
OPTIONS:
  -x, --expand                     show more info for specified components
  --stats                          print useful policy statistics
  -l, --line-breaks                print line breaks in constrain statements
  -h, --help                       print this help text and exit
  -V, --version                    print version information and exit

For component options, if NAME is provided, then only show info for
NAME.  Specifying a name is most useful when used with the -x option.
If no option is provided, display useful policy statistics (-s).

The default source policy, or if that is unavailable the default binary
policy, will be opened if no policy is provided.

</pre>

So based on this we do:


<pre>
$ seinfo --attribute=unconfined_domain_type -x
   unconfined_domain_type
      sosreport_t
      bootloader_t
      devicekit_power_t
      virt_qemu_ga_unconfined_t
      nova_conductor_t
      dirsrvadmin_unconfined_script_t
      timemaster_t
      nova_objectstore_t
      certmonger_unconfined_t
      unconfined_cronjob_t
      unconfined_sendmail_t
      virtd_lxc_t
      abrt_handle_event_t
      setfiles_mac_t
      initrc_t
      fsadm_t
      lvm_t
      rpm_t
      wine_t
      cinder_scheduler_t
      unconfined_dbusd_t
      unconfined_mount_t
      realmd_consolehelper_t
      rtas_errd_t
      preupgrade_t
      authconfig_t
      snapperd_t
      vmware_host_t
      puppetagent_t
      vmtools_helper_t
      phc2sys_t
      pegasus_openlmi_unconfined_t
      prelink_t
      vmtools_t
      anaconda_t
      cinder_volume_t
      boinc_project_t
      cinder_api_t
      cloud_init_t
      rpm_script_t
      system_cronjob_t
      openshift_initrc_t
      samba_unconfined_net_t
      pki_tomcat_script_t
      kdumpctl_t
      unconfined_service_t
      devicekit_disk_t
      zabbix_script_t
      firstboot_t
      keepalived_unconfined_script_t
      samba_unconfined_script_t
      nagios_eventhandler_plugin_t
      httpd_unconfined_script_t
      openvpn_unconfined_script_t
      depmod_t
      insmod_t
      kernel_t
      livecd_t
      realmd_t
      tomcat_t
      watchdog_unconfined_t
      clvmd_t
      crond_t
      inetd_t
      init_t
      mount_t
      ptp4l_t
      tuned_t
      udev_t
      virtd_t
      nagios_unconfined_plugin_t
      pegasus_openlmi_logicalfile_t
      devicekit_t
      inetd_child_t
      <strong>unconfined_munin_plugin_t</strong>
      semanage_t
      sge_shepherd_t
      xdm_unconfined_t
      unconfined_t
      cluster_t
      openwsman_t
      install_t
      sge_job_t
      xserver_t
      condor_startd_t
      cinder_backup_t


</pre>




See also:

http://wiki.centos.org/HowTos/SELinux