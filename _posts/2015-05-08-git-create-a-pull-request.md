---
ID: 386
post_title: 'Git &#8211; Create a pull request'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/git/git-create-a-pull-request
published: true
post_date: 2015-05-08 00:00:00
---


do the following to add new branch to stash:

git clone profiles repo:            git clone ssh://github.com/in/projectname.git
git checkout new-base-profiles

git branch temporary-branch-name         # creates new branch
git checkout temporary-branch-name
# make changes to files.
git add
git commit
git push origin temporary-branch-name
then on stash -> create pull request


source = temporary-branch-name
destination = new-base-profile