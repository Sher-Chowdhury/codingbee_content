---
ID: 321
post_title: 'Git &#8211; Undoing things'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/git/git-undoing-things
published: true
post_date: 2015-03-09 00:00:00
---
There are 3 common things you may want to undo when using git:


<ul>
	<li>undo a commit</li>
	<li>unstage a file</li>
	<li>unmodify a file</li>
</ul>



If you have done several commits and you want to roll back several commits, then do:


<pre>git log -p</pre>


This will list commit id's pick the id you want to roll back to, then do:

<pre>
git reset --hard e0e7671b58895cfa1bbca4016238c693efaa4fbe
</pre>

This will rollback to a commit with the id above. Be careful with this command, because you cannot undo this. 




If you wan to delete a remote branch:

http://stackoverflow.com/questions/2003505/delete-a-git-branch-both-locally-and-remotely

Rename a local and remote branch:

http://stackoverflow.com/questions/1526794/rename-master-branch-for-both-local-and-remote-git-repositories  


http://lrotherfield.com/blog/delete-remote-git-repo-to-specific-commit/ 



if you have unstaged changes of a folder and it's contents, and you want to undo all the changes you made in that folder then do:


<pre>
git checkout -- {folder-name}
</pre>