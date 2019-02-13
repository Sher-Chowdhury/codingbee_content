---
ID: 41
post_title: >
  linux-compress-a-whole-folder-in-linux-using-tar
author: admin
post_excerpt: ""
layout: post
permalink: http://localhost:8000/?p=41
published: true
post_date: 2014-01-05 00:00:00
---
## What is tar

You may have come across the gzip and bzip2 commands which are used for compressing individual files. But if you want to compress a whole directory, then the command you need to use is `tar`. The tar command requires you to declare a number of options for it work, so the best way to understand how tar works is to see it in action:

## Creating a tar file.

The tar command has several different modes, and to generate a tar file, you need to enable the "create" mode. Here's how you compress a whole directory.

<pre>tar -cf {tar-filename} /path/to/dir</pre>

For the above, you can read the options "-cf" as: (c)reate a tar (f)ile with the name {tar-filename}, using the content from /path/to/dir.

If you want to do the above, but also see the progress of the tar activity, then do:

<pre>tar -cvf {tar-filename} /path/to/dir</pre>

For the above, you can read "-cvf" as: (c)reate a tar (f)ile in (v)erbose mode, called {tar-filename}, using the content from /path/to/dir.

## Creating a compressed tar file.

The tar files that we have created so far, are not compressed files. A compressed tar file can be created in 2 steps, like this:

<pre>tar -cf {tar-filename} /path/to/dir        # step 1 - create the tarfile.
gzip {tar-filename}                     # step 2 - compress the tarfile.
</pre>

However you can instruct the tar command to also do the gzipping for you:

<pre>tar -cvzf {tar-filename} /path/to/dir              # Here, tar compresses the tar file using the gzip utility.
</pre>

For the above, you can read "-cvzf" as: (c)reate a g(z)ipped tar (f)ile in (v)erbose mode, called {tar-filename}, using the content from /path/to/dir.

Alternatively, you can use bzip2 utility like this:

<pre>tar -cvjf {tar-filename} /path/to/dir  </pre>           # Here, the tar command compresses the tar file using the bzip2 utility. Note: Options z and j don't work in Solaris 10. 

## Extracting a tar file To extract a tar file, you need to enable the tar command's "extract" mode: 

<pre>tar -xf tarfile.tar</pre> For the above, you can read the options "-xf" as: e(x)tract the tar file of the (f)ilename, tarfile.tar. 

<pre>tar -xvf name.tar  </pre>        # This does the same as above, but in verbose mode. For the above, you can read "-xvf" as: e(x)tract, in (v)erbose, the tar file of the (f)ilename, tarfile.tar. 

## Extracting a zipped tar file So far we have only looked at how to extract an uncompressed tar file. But when dealing with compressed tar files, you can extract them by taking a 2 step approach: 

<pre>gunzip tarfile.tar.gz  </pre>    # step 1 - unzip the compressed tarfile. 

<pre>tar -xvf tarfile.tar  </pre>    # step 2 - extract the tarfile. However it is easier to just do both steps in a single command like this: 

<pre>tar -xvzf tarfile.tar.gz</pre> For the above, you can read "-xvzf" as: gun(z)ip and then e(x)tract, in (v)erbose mode, the tar file, of the (f)ilename, tarfile.tar.gz Similarly, if you are dealing with tar files that have been compressed with bzip2, you do: 

<pre>tar -xjvf tarfile.tar.bzip2</pre>

### Also see How to compress a Whole Directory in Linux? (Coming soon) The 5 main modes of tar (Coming soon) 20 practical example on how to use tar (Coming soon)