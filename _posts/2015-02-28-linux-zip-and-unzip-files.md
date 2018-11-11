---
ID: 307
post_title: 'Linux &#8211; Zip and unzip files'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/linux-zip-and-unzip-files
published: true
post_date: 2015-02-28 00:00:00
---
To zip a single file a in linux
<pre>gzip filename</pre>

To unzip a single file in linux 
<pre> gunzip filename </pre>

To compress a directory the command is 
<pre> tar -cf filename.tar filename</pre>
filename.tar will be the new name of zipped file for filename.
-cf means creat a file
When compressing a directory it is important not to be in the directory. The command will not work otherwise.

To extract the filename.tar file the command is 
<pre> tar -xf goat.tar</pre>
-xf means extracting a file.