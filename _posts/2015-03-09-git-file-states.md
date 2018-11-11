---
ID: 316
post_title: 'Git &#8211; File states'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/git/git-file-states
published: true
post_date: 2015-03-09 00:00:00
---
Each of the files in the working directory are in one of two high-level states:


<ol>
	<li><strong>tracked files</strong> - These are files that were in the last snapshot (aka); they can be in one of the following sub states:
<ol>

	<li><strong>unmodified</strong> - These are any files that haven't been modified since the last commit. They will still be included in the next commit, but remain as is.</li>
	<li><strong>modified</strong> - These are files that have been modified since the last commit (we probably modified these as part of bug fixes). These files will be included in the next commit, but will be included in thier respective new form. </li>
	<li><strong>staged</strong> - These are files that are either not present in the last commit (e.g. newly created files) or are "modified" files that we tell git to include in the next commit. Files are added to the staging using git's "add" command.  </li>
</li>
</ol>

	<li><strong>untracked files</strong> - these are any files that are not being tracked, i.e. git isn't of the existance of these files. You can change the state of these files to "tracked - staged" by using git's "add" command</li>
</ol>

<h3>
In the case of cloning an exisiting git repo</h3>
Now if you clone a git repo, then all the project files start off in the "unmodified" state
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

If you edit any of these files (e.g using a text editor such as vim), then these file's state changes to "modified"

<pre>

                  |----------------------Tracked----------------------------| 
                  |                                                         |
Untracked             Unmodified                 Modified                Staged 
    |                      |                      |                     |
    |                      |                      |                     |
    |                      |                      |                     |
    |                      | edit a tracked file  |                     |
    |                      | -------------------> |                     |
    |                      |                      |                     |
    |                      |                      |                     |
    |                      |                      |                     |
    |                      |                      |                     |
    |                      |                      |                     |
    |                      |                      |                     |

</pre>


At this stage, if you do a git "commit", the edited file will not get committed, that's because modified files first needs to be "staged" in order to be included in the next commit. This is done using git's "add" command:


<pre>

                  |----------------------Tracked----------------------------| 
                  |                                                         |
Untracked             Unmodified                 Modified                Staged 
    |                      |                      |                     |
    |                      |                      |                     |
    |                      |                      |                     |
    |                      | edit a tracked file  |                     |
    |                      | -------------------> |                     |
    |                      |                      | "add" modified files|
    |                      |                      | ------------------->|
    |                      |                      |                     |
    |                      |                      |                     |
    |                      |                      |                     |
    |                      |                      |                     |

</pre>


You also use git's "add" command to have new files "staged" so that they are included in the the next commit:


<pre>

                  |----------------------Tracked----------------------------| 
                  |                                                         |
Untracked             Unmodified               Modified               Staged 
    |                      |                      |                     |
    | Add new files to be included in the next commit                   |
    | ----------------------------------------------------------------->|
    |                      |                      |                     |
    |                      | edit tracked files   |                     |
    |                      | -------------------> |                     |
    |                      |                      | "add" modified files|
    |                      |                      | ------------------->|
    |                      |                      |                     |
    |                      |                      |                     |
    |                      |                      |                     |
    |                      |                      |                     |

</pre>

After that, once you are happy with everything, you then take a snapshot using git's "commit" command. This results in the files from the previous commit being overlayed with the newly staged files in order to create a new snapshot (aka commit). 

You then repeat the cycle again. 

Another thing you may want to do is remove a tracked file from the project. This is done using git's rm command:


<pre>

                  |----------------------Tracked----------------------------| 
                  |                                                         |
Untracked             Unmodified               Modified               Staged 
    |                      |                      |                     |
    | Add new files to be included in the next commit                   |
    | ----------------------------------------------------------------->|
    |                      |                      |                     |
    |                      | edit tracked files   |                     |
    |                      | -------------------> |                     |
    |                      |                      | "add" modified files|
    |                      |                      | ------------------->|
    |  remove file         |                      |                     |
    | <------------------- |                      |                     |
    |                      |                      |                     |
    |                      |                      |                     |

</pre>
   


When you are making edit's to a tracked files, git behind the scenes of what files are being modified. Hence when we check the status of a newly cloned repo, we see:

<pre>
[vagrant@localhost puppetlabs-ntp]$ git status
# On branch master
nothing to commit (working directory clean)
[vagrant@localhost puppetlabs-ntp]$

</pre>

However when we edit a unmodified-tracked file and check the status again:

<pre>
[vagrant@localhost puppetlabs-ntp]$ ls
CHANGELOG.md     Gemfile  LICENSE    metadata.json  README.markdown  templates
CONTRIBUTING.md  lib      manifests  Rakefile       spec             tests
[vagrant@localhost puppetlabs-ntp]$ echo "hello" >> README.markdown
[vagrant@localhost puppetlabs-ntp]$ git status
# On branch master
# Changed but not updated:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#       modified:   README.markdown
#
no changes added to commit (use "git add" and/or "git commit -a")
[vagrant@localhost puppetlabs-ntp]$

</pre>

As you can see we also get prompts on how to stage this file in order to be included in the next commit.

Now let's create a new file that is going to be part of the project:


<pre>
[vagrant@localhost puppetlabs-ntp]$ echo "hello" >> testfile.txt
[vagrant@localhost puppetlabs-ntp]$ git status
# On branch master
# Changed but not updated:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#       modified:   README.markdown
#
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#       testfile.txt
no changes added to commit (use "git add" and/or "git commit -a")
[vagrant@localhost puppetlabs-ntp]$
</pre> 


As you can see, git has also discovered this new file, and is marked it as a an untracked file, along with how to get this filed as tracked-staged so to have it included in the next commit. 

If we now go ahead and do a commit, you'll discover from git's status that nothing has actually happened, since nothing has been put into staging:


<pre>

[vagrant@localhost puppetlabs-ntp]$ git commit
# On branch master
# Changed but not updated:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#       modified:   README.markdown
#
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#       testfile.txt
no changes added to commit (use "git add" and/or "git commit -a")
[vagrant@localhost puppetlabs-ntp]$ git status
# On branch master
# Changed but not updated:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#       modified:   README.markdown
#
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#       testfile.txt
no changes added to commit (use "git add" and/or "git commit -a")
[vagrant@localhost puppetlabs-ntp]$


</pre>




Let's now follow the above instructions to stage these 2 files:
<pre>
[vagrant@localhost puppetlabs-ntp]$ git add README.markdown
[vagrant@localhost puppetlabs-ntp]$ git add testfile.txt
[vagrant@localhost puppetlabs-ntp]$ git status
# On branch master
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#
#       modified:   README.markdown
#       new file:   testfile.txt
#
[vagrant@localhost puppetlabs-ntp]$ git commit -m "this is a demo"
[master 62cdcc6] this is a demo
 2 files changed, 2 insertions(+), 0 deletions(-)
 create mode 100644 testfile.txt
[vagrant@localhost puppetlabs-ntp]$ git status
# On branch master
# Your branch is ahead of 'origin/master' by 1 commit.
#
nothing to commit (working directory clean)
[vagrant@localhost puppetlabs-ntp]$

</pre>

Now everything is committed. 

Note, at this point, our commit is only stored on our local workstation. We need to do a git "push" to get our commit uploaded.


Tips:

There will be times when you have a lot of modified files that you want staged. In which case, it can get tedious to stage them one-by-one. However you can use the following to stage all modified files:

<pre>
git add -u
</pre>

If you want to stage all modified files as well as all untracked files, then do:


<pre>
git add -A
</pre> 

Note, this command will also factor in any tracked files that have been set to untracked too. 

Really useful link: http://stackoverflow.com/questions/572549/difference-between-git-add-a-and-git-add

Note, there isn't any obvious way to only stage new files and ignore any modified files. 
 

<h3>
In the case of creating a new git repo</h3>


With the new git repo, there are no commits to begin with. Hence all exisitng/newly-written project files start out as untracked files. We then use the "add" command to tracked-staged them, before we do the commit.