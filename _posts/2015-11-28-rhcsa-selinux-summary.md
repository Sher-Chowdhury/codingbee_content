---
ID: 489
post_title: SELinux Summary
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-selinux-summary
published: true
post_date: 2015-11-28 00:00:00
---
The policy book can be thought of as a really big text book, which contains 3 chapters.

Chapter 1 - Contains a list of all available security attributes. There are actually  4 lists, one for each type of the differnt types of suecurity attributes user:role:type:level. In the targeted policy, the "type" list is by far the longest, containing about 4500 entries.  
Chapter 2 - lists mapping rules about which security attribute can access to which other security attributes. 
chapter 3 - list selinux context assignment rules. This chapter gives info about what SELinux needs, in order to determine what selinux context values every object in your machine should have.
   


<h2>Overview</h2>
By the end of this article you should be able to answer the following questions:

[accordion]
[toggle title="What is the command to list all available security attributes?"]
seinfo          # this retrieves info from the policy book
[/toggle]
[toggle title="What command do you use to see which selinux attributes are allowed access to objects of which other selinux attributes?"]
sesearch
[/toggle]
[toggle title="What is the command to see the SELinux logic for assigning selinux contexts to objects?"]
semanage        # this retrieves info from the policy book
[/toggle]
[toggle title="What is the command to modify selinux context assignment rules (i.e. chapter 3)?"]
semaage
[/toggle]
[toggle title="What is the command?"]

[/toggle]
[toggle title="What is the command?"]

[/toggle]
[toggle title="What is the command?"]

[/toggle]
[toggle title="What is the command?"]

[/toggle]
[toggle title="What is the command?"]

[/toggle]
[toggle title="What is the command?"]

[/toggle]
[toggle title="What is the command?"]

[/toggle]
[toggle title="What is the command?"]

[/toggle]
[toggle title="What is the command?"]

[/toggle]
[/accordion]

<hr/>