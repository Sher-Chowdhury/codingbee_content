---
ID: 331
post_title: 'Git &#8211; The 3 main parts of git'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/git/git-the-3-main-parts-of-git
published: true
post_date: 2015-03-22 00:00:00
---
A git project is made up of 3 parts:


<ol>
	<li><strong>Working directory</strong> - This is the directory where all your project files and folders reside (along with the .git folder). Each of your files within this directory is in 1 of possible states, untracked, unmodified, modified, staged. Will cover more about <a href="http://codingbee.net/tutorials/git/git-file-states/" title="Git - File states">file states</a> later.</li>
	<li><strong>staging area</strong> - This is a hypothetical layer which sit's on top of the last commit's layer. When you run the "git commit" command the 2 layers get's merged. Any files that are in the commit layer, that has a newer file directly above it (in the "staging layer"), will get over-written by the newer (staged) file. Note, that a file's content are tracked so that you can roll back to how the file looked like in any previous (commits) snapshots. Note the staging area is also referred to as  the "index"</li>
	<li><strong>.git directory</strong> - this is basically like your git repo's database. </li>
</ol>



<pre>
 Working                 Staging             .git directory 
Directory                 area                (Repository)
    |                       |                     |
    |                       |                     |
    |                       |                     |
    |                       |                     |
    |                       |                     |
    |                       |                     |
    |                       |                     |
    |                       |                     |
    |                       |                     |
    |                       |                     |
    |                       |                     |
</pre> 


If you want to get a new file tracked by the git repository, then you do the following: 

<ol>
	<li>Use the "git checkout" command to checkout the project. This will pull out the latest commit (snapshot) from the .git repositorie's database (for a particular branch) and place it into the working directory. All the newly checked out files have the file state "unmodified" (more about file states later).

<pre>
 Working                 Staging             .git directory 
Directory                 area                (Repository)
    |                       |                     |
    |      git checkout     |                     |
    | <========================================== |
    |                       |                     |
    |                       |                     |
    |                       |                     |
    |                       |                     |
    |                       |                     |
    |                       |                     |
    |                       |                     |
    |                       |                     |
    |                       |                     |
</pre> 


</li>
        <li>add/create the file somewhere inside the working-directory. This will make git aware of the existence of this file it won't keep track of this file. i.e. the file's state is "untracked" (more about file states later).</li>
	<li>Use the "git add" command to place this file in the staging area, waiting to be merged into the previous commit (snapshot). This will change the file's state to "staged" (more about file states later).

<pre>
 Working                 Staging             .git directory 
Directory                 area                (Repository)
    |                       |                     |
    |      git checkout     |                     |
    | <========================================== |
    |                       |                     |
    |     git add           |                     |
    | =====================>|                     |
    |                       |                     |
    |                       |                     |
    |                       |                     |
    |                       |                     |
    |                       |                     |
    |                       |                     |
</pre> 



</li>
	<li>Use the "git commit" command to add the file, to create a new snapshot which is made up from the previous commit "layer" along with the files in the "staged layer". This will change the file's state to "unmodified" (more about file states later).

<pre>
 Working                 Staging             .git directory 
Directory                 area                (Repository)
    |                       |                     |
    |      git checkout     |                     |
    | <========================================== |
    |                       |                     |
    |     git add           |                     |
    | =====================>|                     |
    |                       |                     |
    |                       |   git commit        |
    |                       | ===================>|
    |                       |                     |
    |                       |                     |
    |                       |                     |
</pre> 


</li>
</ol>


 Here's another way to look at the above:

<a href="http://codingbee.net/wp-content/uploads/2015/03/Ne1EPj7.png"><img src="http://codingbee.net/wp-content/uploads/2015/03/Ne1EPj7.png" alt="" width="1024" height="768" class="alignnone size-full wp-image-3529" /></a>











<pre>
                  |----------------------Tracked----------------------------| 
                  |                                                         |
Untracked             Unmodified             Modified                Staged 
    |                      |                     |                     |
    |                      |                     |                     |
    |                      |                     |                     |
    |                      |                     |                     |
    |                      |                     |                     |
    |                      |                     |                     |
    |                      |                     |                     |
    |                      |                     |                     |
    |                      |                     |                     |
    |                      |                     |                     |
    |                      |                     |                     |
</pre>