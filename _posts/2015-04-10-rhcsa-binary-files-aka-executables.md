---
ID: 352
post_title: 'RHCSA &#8211; Binary files (aka executables)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-binary-files-aka-executables
published: true
post_date: 2015-04-10 00:00:00
---
In Linux a lot of files, especially the underlying files represented by shell commands are in the form of a non-human readable binary file, which is also referred to as an executable. The humble "pwd" command is a typical example:


<pre>
$ type pwd
pwd is a shell builtin
$ which pwd
/usr/bin/pwd
</pre>

The above "pwd" file appears to be binary file, since it's in the (bin)ary folder. But this may not always be the case. A better way to determine whether a file is a binary/executable or not is by using the "file" command.   


<h2>The "file" command</h2>

<pre>
$ whatis file
file (1)             - determine file type
</pre>

Hence:

<pre>
$ file /usr/bin/pwd
/usr/bin/pwd: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.32, BuildID[sha1]=0x9a42cda95a4835fcef88198eecf0bd7191e8d3dc, stripped
</pre> 



<h2>the "ldd" command</h2>

The people who have developed binary files, such as pwd, usually re-use code provided by others. These third party codes provided by others are usually packaged into "libraries".

The ldd (List Dynamic Dependencies) command displays the shared libraries required by each program:


<pre>
$ whatis ldd
ldd (1)              - print shared library dependencies
$ ldd --help
Usage: ldd [OPTION]... FILE...
      --help              print this help and exit
      --version           print version information and exit
  -d, --data-relocs       process data relocations
  -r, --function-relocs   process data and function relocations
  -u, --unused            print unused direct dependencies
  -v, --verbose           print all information

For bug reporting instructions, please see:
<http://www.gnu.org/software/libc/bugs.html>.

</pre>
 
Hence for pwd, we have:


<pre>$ ldd /usr/bin/pwd
    linux-vdso.so.1 =>  (0x00007fffadbfe000)
    libc.so.6 => /lib64/libc.so.6 (0x00007f5a9f34d000)
    /lib64/ld-linux-x86-64.so.2 (0x00007f5a9f721000)
</pre>



See also

http://www.cyberciti.biz/tips/linux-shared-library-management.html

http://www.ibm.com/developerworks/linux/library/l-dynamic-libraries/