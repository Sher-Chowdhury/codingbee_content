---
ID: 182
post_title: 'Ruby &#8211; Protected Methods'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-protected-methods
published: true
post_date: 2014-11-14 00:00:00
---
Protected methods is something that is very rarely used, so I have skipped this for now. 


Lets say you have two obects in instantiated from the same class:

- objectA
- objectB

Let's also say you have a protected method called "prot_method". 

In order to apply this method on objectB, then you can do it indirectly via applying a method to objectA, and passing objectB as a parameter into that object. 

See "excerpt section of this post"