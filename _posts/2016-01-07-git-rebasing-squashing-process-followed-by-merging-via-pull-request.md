---
ID: 499
post_title: 'Git &#8211; rebasing + squashing process, followed by merging via pull request'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/git/git-rebasing-squashing-process-followed-by-merging-via-pull-request
published: true
post_date: 2016-01-07 00:00:00
---
Let's say I have a feature branch called "feature-x" which I want to first rebase onto the "develop" branch. Then it is first best practice to squash all my feature-branch's commits into a single commit and then merge it into the develop branch via a pull request.

first checkout the develop branch and pull in the latest changes.

<pre>
$ git checkout develop
$ git pull
</pre>

Now do the rebasing:

<pre>
$ git checkout feature-x
$ git rebase -i develop 
</pre>

At this stage just use vim's "wq" option to save and quit. This does the rebasing without any squashing.

Now we do the squashing, using the "f" option, for each commit:

<pre>
$ git rebase -i develop
</pre>

Now we use the following to create a new overall commit message:

<pre>
$ git commit -v --amend
</pre>

Tip: Here are some advice on <a href="http://chris.beams.io/posts/git-commit/#imperative" rel="nofollow">how to write good commit messages</a>.


Now check everything looks ok:

<pre>
$ git log -p
$ git log -p -n1 # displays info about only the latest commit.
</pre>

Now update your git commit's timestamp:

<pre>
$ git commit --amend --date="$(date -R)"
</pre>

Now push your changes to your bitbucket/github server. the following overwrites the entire history of the current branch. Hence it replaces the individual commits with squashed commits.

<pre>
$ git push --force origin feature-x
</pre>

# login into your git server using firefox, and then create the pull request.

Some useful links:
https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History
https://www.atlassian.com/git/tutorials/rewriting-history

The aim of this approach is that it you don't end up cluttering up your main master branch with lots of commits.