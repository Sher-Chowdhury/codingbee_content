---
ID: 402
post_title: 'RHCSA &#8211; Kernel Modules'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-kernel-modules
published: true
post_date: 2015-05-24 00:00:00
---
<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="True or False, the kernel can directly interact with all hardware?"]
False - it can only directly interact with the core hardware only. With remaining hardware, it interacts via kernel modules. 
[/toggle]
[toggle title="What is the command to list all modules that are currently being used?"]
$ lsmod
[/toggle]
[toggle title="What is the command list all kernel modules, and not just the active ones?"]
$ find /lib/modules/$(uname -r)/kernel -type f -iname '*.o' -or -iname '*.ko'
[/toggle]
[toggle title="What is the command to load in the 'bluetooth'?"]
$ modprobe -v bluetooth
[/toggle]
[toggle title="What is the unload the bluetooth module?"]
$ modprobe -rv bluetooth
[/toggle]
[toggle title="What is the command to view more info about the 'cdrom', kernel module, along with it's parameter/boolean settings?"]
$ modinfo cdrom
[/toggle]
[toggle title="What is the command to load in the 'cdrom' kernel module but this time with the 'autoclose' boolean setting enabled?"]
$ modprobe -v cdrom autoclose=1
[/toggle]
[toggle title="The above isn't persistant so what do you need to do make it persistant, (and also set the 'lockdoor' boolean setting to disabled)?"]
go to the follwing directory:
/lib/modprobe.d
Create a file with a meaningful name and add the following content:
$ cat /lib/modprobe.d/cdrom.conf
options cdrom autoclose=1 lockdoor=0
# then reboot the machine
[/toggle]
[toggle title="Where can you find more info about the modprobe.d folder?"]
$ man modprobe.d
[/toggle]
[toggle title="What is the command to check the 'autoclose' setting for the cdrom kernel module?"]
sysctl -a | grep cdrom | grep autoclose
[/toggle]
[/accordion]

<hr/>




The Linux kernel is the heart of the RHEL Operating System. You can use the uname command to find the kernel's version:


<pre>
$ uname -r
3.10.0-229.1.2.el7.x86_64
</pre>





In order for the kernel to perform it's duties, it has to interact with the machines hardware. The kernel has the ability to directly interact with most of the core hardware. However with other hardware, e.g. usb-pen, the kernel interacts with them via "kernel modules". That's so to keep the kernel as light weight as possible.  

We also have a service called udevd:


<pre>
$ systemctl status systemd-udevd -l
systemd-udevd.service - udev Kernel Device Manager
   Loaded: loaded (/usr/lib/systemd/system/systemd-udevd.service; static)
   Active: active (running) since Sun 2015-05-24 14:11:44 BST; 3h 57min ago
     Docs: man:systemd-udevd.service(8)
           man:udev(7)
 Main PID: 552 (systemd-udevd)
   CGroup: /system.slice/systemd-udevd.service
           └─552 /usr/lib/systemd/systemd-udevd

May 24 14:11:44 localhost.localdomain systemd[1]: Started udev Kernel Device Manager.
</pre> 

This service constantly monitors for any new hardware that is added to the machine (e.g. external usb hard drive), and soon as it detects the new hardware, it then notifies the kernel with the details of the new device, and the kernel then loads in the necessary kernel modules so that it can start interacting and working with the new hardware. 


You can use the lsmod to view a list of kernel modules that are currently in use:


<pre>
$ lsmod
Module                  Size  Used by
xt_CHECKSUM            12549  1
ipt_MASQUERADE         12678  3
nf_nat_masquerade_ipv4    13412  1 ipt_MASQUERADE
tun                    27183  1
ip6t_rpfilter          12546  1
ip6t_REJECT            12939  2
ipt_REJECT             12541  4
xt_conntrack           12760  8
ebtable_nat            12807  0
ebtable_broute         12731  0
bridge                115385  1 ebtable_broute
stp                    12976  1 bridge
llc                    14552  2 stp,bridge
ebtable_filter         12827  0
ebtables               30913  3 ebtable_broute,ebtable_nat,ebtable_filter
ip6table_nat           12864  1
nf_conntrack_ipv6      18738  5
nf_defrag_ipv6         34651  1 nf_conntrack_ipv6
nf_nat_ipv6            14131  1 ip6table_nat
ip6table_mangle        12700  1
ip6table_security      12710  1
ip6table_raw           12683  1
ip6table_filter        12815  1
ip6_tables             27025  5 ip6table_filter,ip6table_mangle,ip6table_security,ip6table_nat,ip6table_raw
iptable_nat            12875  1
nf_conntrack_ipv4      14862  5
nf_defrag_ipv4         12729  1 nf_conntrack_ipv4
nf_nat_ipv4            14115  1 iptable_nat
nf_nat                 26146  3 nf_nat_ipv4,nf_nat_ipv6,nf_nat_masquerade_ipv4
nf_conntrack          105702  7 nf_nat,nf_nat_ipv4,nf_nat_ipv6,xt_conntrack,nf_nat_masquerade_ipv4,nf_conntrack_ipv4,nf_conntrack_ipv6
iptable_mangle         12695  1
iptable_security       12705  1
iptable_raw            12678  1
iptable_filter         12810  1
ip_tables              27239  5 iptable_security,iptable_filter,iptable_mangle,iptable_nat,iptable_raw
ppdev                  17671  0
snd_intel8x0           38274  3
snd_ac97_codec        130476  1 snd_intel8x0
ac97_bus               12730  1 snd_ac97_codec
snd_seq                63074  0
snd_seq_device         14497  1 snd_seq
snd_pcm               103996  2 snd_ac97_codec,snd_intel8x0
serio_raw              13462  0
pcspkr                 12718  0
snd_timer              29562  2 snd_pcm,snd_seq
snd                    75127  12 snd_ac97_codec,snd_intel8x0,snd_timer,snd_pcm,snd_seq,snd_seq_device
parport_pc             28165  0
parport                42348  2 ppdev,parport_pc
video                  19263  0
i2c_piix4              22106  0
i2c_core               40325  1 i2c_piix4
soundcore              15047  1 snd
nfsd                  290215  1
auth_rpcgss            59343  1 nfsd
nfs_acl                12837  1 nfsd
lockd                  93977  1 nfsd
uinput                 17625  0
sunrpc                295293  7 nfsd,auth_rpcgss,lockd,nfs_acl
xfs                   915019  3
libcrc32c              12644  1 xfs
sr_mod                 22416  0
cdrom                  42556  1 sr_mod
sd_mod                 45499  6
crc_t10dif             12714  1 sd_mod
crct10dif_common       12595  1 crc_t10dif
ata_generic            12910  0
pata_acpi              13038  0
ahci                   29870  4
libahci                32009  1 ahci
ata_piix               35038  0
e1000                 149270  0
libata                218854  5 ahci,pata_acpi,libahci,ata_generic,ata_piix
dm_mirror              22135  0
dm_region_hash         20862  1 dm_mirror
dm_log                 18411  2 dm_region_hash,dm_mirror
dm_mod                104038  13 dm_log,dm_mirror
</pre>


Unfortunately there isn't a command that lets you view a list of all modules (loaded and unloaded), therefore we resort to using the "find" command instead: 

<pre>
$ find /lib/modules/$(uname -r)/kernel -type f -iname '*.o' -or -iname '*.ko' | wc -l
2182
</pre>



You can also manually load kernel modules using the modprobe command, for example, let's say you want to load the blueotooth module, first let's check that the module is available for loading:


<pre>
$ find /lib/modules/$(uname -r)/kernel -type f -iname '*.o' -or -iname '*.ko' | grep "bluetooth.ko"
/lib/modules/3.10.0-229.1.2.el7.x86_64/kernel/drivers/platform/x86/toshiba_bluetooth.ko
/lib/modules/3.10.0-229.1.2.el7.x86_64/kernel/net/bluetooth/bluetooth.ko
</pre>

Now let's confirm that it isn't currently loaded:


<pre>
$ lsmod | grep "bluetooth"
</pre>


Now let's load the module, which is done using the modprobe command:


<pre>
$ modprobe -v bluetooth
insmod /lib/modules/3.10.0-229.1.2.el7.x86_64/kernel/net/rfkill/rfkill.ko
insmod /lib/modules/3.10.0-229.1.2.el7.x86_64/kernel/net/bluetooth/bluetooth.ko
$ lsmod | grep bluetooth
bluetooth             372662  0
rfkill                 26536  1 bluetooth
</pre>

Note, I used the (v)erbose to see what's happening. 

Here we can see that that no other modules are dependant on the bluetooth module (indicate by 0) but the bluetooth module is dependant on the rfkill module, which also got loaded behind the scenes. Only one module is dependent on the rfkill (indicated by the 1). 


To unload a module, you use the modprobe again but this time using the (r)emove switch:


<pre>
$ modprobe -rv bluetooth
rmmod bluetooth
rmmod rfkill
$ lsmod | grep bluetooth
</pre>

As you can see this also unloaded the rfkill module since it got loaded up as part of the bluetootch, and also because only the bluetooth module depended on the rfkill module. 


If you try to unload a module that other modules are dependent, then you'll get an error message like this:


<pre>
$  modprobe -rv cdrom
modprobe: FATAL: Module cdrom is in use.
</pre>

Now let's check it's dependencies, we see that:


<pre>
$ lsmod | grep cdrom
cdrom                  42556  1 sr_mod
</pre>

Here we see that the sr_mod modules depends on the cdrom module, if we take a look at the we see:


<pre>
$ lsmod | grep sr_mod
sr_mod                 22416  0
cdrom                  42556  1 sr_mod

</pre>

As you can see, no other modules depends on sr_mod, so we can try unloading the sr_mod module first:

<pre>
$ modprobe -rv sr_mod
rmmod sr_mod
rmmod cdrom
$ lsmod | grep sr_mod
$ lsmod | grep cdrom
</pre>
 
Since the cdrom was only being used via sr_mod, it also got unloaded behind the scenes as well.  

In reality it is very rare that you will ever need to manually load/unload kernel modules because the udevd service does a really good job doing this for you. 

<h2>Monitor the udev service's realtime logs</h2>

The main process underneath the udevd service is the udec

You can monitor in real time which modules are being loaded/unloaded using the udevadm command along with the "monitor" option. To do this, I opened up a separate terminal session to run the monitor the output while I ran "modprobe -rv sr_mod" command in my first terminal:

<pre>
$ udevadm monitor
monitor will print the received events for:
UDEV - the event which udev sends out after rule processing
KERNEL - the kernel uevent

KERNEL[70482.463906] remove   /devices/virtual/bdi/11:0 (bdi)
KERNEL[70482.468373] remove   /devices/pci0000:00/0000:00:01.1/ata2/host1/target1:0:0/1:0:0:0/block/sr0 (block)
KERNEL[70482.471845] remove   /bus/scsi/drivers/sr (drivers)
KERNEL[70482.471969] remove   /module/sr_mod (module)
UDEV  [70482.483339] remove   /devices/virtual/bdi/11:0 (bdi)
UDEV  [70482.486603] remove   /bus/scsi/drivers/sr (drivers)
UDEV  [70482.486662] remove   /module/sr_mod (module)
KERNEL[70482.496314] remove   /module/cdrom (module)
UDEV  [70482.503934] remove   /devices/pci0000:00/0000:00:01.1/ata2/host1/target1:0:0/1:0:0:0/block/sr0 (block)
UDEV  [70482.504962] remove   /module/cdrom (module)
</pre>

The above is the output from the udevd service. If I then attached a new device, e.g. usb-pen, then you can use the "udevadm monitor" to see what modules are being loaded in behind the scenes.  
 



<h2>Finding kernel module details and adjusting kernel module parameter settings</h2>
You can use the modinfo command to get more details about any kernel module, irrespective of whether they are currently loaded or not, for example:

<pre>
$ modinfo cdrom
filename:       /lib/modules/3.10.0-229.1.2.el7.x86_64/kernel/drivers/cdrom/cdrom.ko
license:        GPL
rhelversion:    7.1
srcversion:     EB46A7E87598E0DD56A115E
depends:
intree:         Y
vermagic:       3.10.0-229.1.2.el7.x86_64 SMP mod_unload modversions
signer:         CentOS Linux kernel signing key
sig_key:        34:B5:BC:A2:B7:06:D8:2E:72:A5:BE:3E:E4:09:BE:C7:19:5E:A5:08
sig_hashalgo:   sha256
parm:           debug:bool
parm:           autoclose:bool
parm:           autoeject:bool
parm:           lockdoor:bool
parm:           check_media_type:bool
parm:           mrw_format_restart:bool

</pre>


You can pass various parameters while loading kernel modules. The modinfo command lists a kernel module's available parameters which are listed as "parm" entries. For example for the cdrom kernel module, the last 6 lines shows the list of  parameters that are available. 

Note for boolean parameters, 0=not-enabled and 1=enabled. 


Hence we can enable the cdrom's kernel module's autoclose feature like this:


<pre>
$ modprobe -v cdrom autoclosex=1
insmod /lib/modules/3.10.0-229.1.2.el7.x86_64/kernel/drivers/cdrom/cdrom.ko autoclosex=1
modprobe: ERROR: could not insert 'cdrom': Unknown symbol in module, or unknown parameter (see dmesg)
</pre>

Here it failed because I had a small typo, I used "autoclosex" instead of "autoclose". Now without the typo: 

<pre>
$ modprobe -v cdrom autoclose=1
insmod /lib/modules/3.10.0-229.1.2.el7.x86_64/kernel/drivers/cdrom/cdrom.ko autoclose=1

</pre>

However if the module is already loaded in then you need to unload it first before loading with the parameter settings. 


This is how you set parameters by manually loading the kernel module. However more importantly you will want to automate this during boot time (i.e. make it persistent). To do this you need to create/edit a *.conf file in:


<pre>
$ ls -l /lib/modprobe.d
total 20
-rw-r--r--. 1 root root  79 Mar  6 05:17 cxgb3.conf
-rw-r--r--. 1 root root  79 Mar  6 05:17 cxgb4.conf
-rw-r--r--. 1 root root 382 Mar  5 19:35 dist-alsa.conf
-rw-r--r--. 1 root root 884 Mar  6 03:02 dist-blacklist.conf
-rw-r--r--. 1 root root 496 Mar  6 05:17 libmlx4.conf

</pre>
 
You can find more info about what this directory is used for in the man pages:

<pre>
$ man 5 modprobe.d
</pre>


So for the above example, I created the following file:


<pre>
$ cat /lib/modprobe.d/cdrom.conf
options cdrom autoclose=1 lockdoor=0
</pre>

Note: I also added another option, to give you an idea on how to specify more than one options. 

I also named this file "cdrom.conf", but in actual fact you give it any name you like. Although it's a good idea to name it after the kernel module. 

Also if you include a typo here then the kernel module will fail to load. 


Unfortunately there is no definitive way to check what the parameter settings are for a given kernel module that is loaded. One possible way is to check the contents of <code>/sys/module</code> folder. This folder has a folder that's named after each kernel module that is currently loaded. So if you reload a kernel module then the corresponding folder is deleted and recreated, e.g.:

<pre>
$ ls -l /sys/module/ | grep cdrom
drwxr-xr-x. 5 root root 0 May 25 15:29 cdrom
$ modprobe -r cdrom
$ ls -l /sys/module/ | grep cdrom
$ modprobe cdrom
$ ls -l /sys/module/ | grep cdrom
drwxr-xr-x. 5 root root 0 May 25 16:01 cdrom

</pre>


Each of these modules folder may have contain a "parameters" folder


<pre>
$ find /sys/module/ -name parameters -type d
/sys/module/vt/parameters
/sys/module/hid/parameters
/sys/module/snd/parameters
/sys/module/tpm/parameters
.
.
...etc
</pre>

The parameters folder contains info about which what parameters are currently set to. However this info is not provided for all kernel modules, since it is something that the kernel module's developer chooses whether or not to implement. 

In the case of the cdrom kernel module, the parameter folder is unfortunately not something that's provided. Failing that you could try checking the /var/log/message file. If that also fails then you can try using sysctl command:


<pre>
$ sysctl -a | grep autoclose
dev.cdrom.autoclose = 1
</pre>

We will cover more about sysctl in the next lesson.