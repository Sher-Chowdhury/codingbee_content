---
ID: 332
post_title: 'Git &#8211; Removing files'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/git/git-removing-files
published: true
post_date: 2015-03-22 00:00:00
---
If there is file that is being tracked by git, but is a file you now want to delete. Then the best way to do it is by running the following git command:

<pre>
$ git remove filename.txt
</pre>
 
This command does 2 things, it first changes the file's state to untrack, and then it actually deletes the file from the working directory altogether, i.e. it does the equivalent of "rm filename.txt". 


You can still remove the file using the conventional way of "rm filename.txt", but you would then have to stage the deletion of the file as well, hence you have to take a extra step with this approach.

Irrespective of the approach you take, you will then need to commit this to confirm the deletion. 


If you just want to remove a file from being tracked, but still keep the actual file, i.e. you want to change a file's state from staged/unmodified/modified to untracked. then you use the "cached" option:


 
<pre>
$ git remove --cached filename.txt
</pre>