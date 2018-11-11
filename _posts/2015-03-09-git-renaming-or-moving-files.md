---
ID: 319
post_title: 'Git &#8211; Renaming or moving files'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/git/git-renaming-or-moving-files
published: true
post_date: 2015-03-09 00:00:00
---
The best way to move or rename a file is doing it a via git's mv command, e.g.:

<pre>$ git mv filename.txt filename2.txt</pre>

This will not only make git aware that the file has been moved (or in this case renamed), but it will actually rename the file in the working directory. 

You can still use the traditional mv command, but this won't get picked up git, and so you have to manually tell git of this, like this:


<pre>
mv filename.txt filename2.txt
git rm filename.txt
git add filename2.txt
</pre>


Hence the <code>git mv</code> command can do all of the above, but in a single line. 


Finally you have to commit the change to take a snapshot for the above to be captured in git's internal db:


<pre>
git commit -m "filename.txt has been renamed to filename2.txt"
</pre>