---
ID: 456
post_title: c-an-intro-to-arrays-and-generics
author: admin
post_excerpt: ""
layout: post
permalink: http://localhost:8000/?p=456
published: true
post_date: 2014-05-10 00:00:00
---
Here are some terminologies:

Arrays: This is a object variable, that is designed to store a collection of object-variable. Each object variable is stored in numbered containers (i.e. they are indexed), with the first container starting at number 0.

Generics: these are like arrays, but they can only store builtin c# variables, i.e. variables that are int, string, double,...etc. However you can't add new items to an array.

There are different types of generic collections:

*   List - these are like arrays, but you can also add new items to the list. Hence you can grow the list by using the "add" keyword
*   Queues - This is based on the idea that the first item in the queue gets processed first....i.e. it is based on first-in, first-out concept.
*   Stack - This is like queues, except it is based in last-in, first-out. Think of it as a stack pancake, where the last pancaked stacked on the pile is also the first pancaked that gets eaten.
*   Dictionaries - These are essentially hash-tables, i.e key/value pairing. This is very powerful, because you can search/sort keys, which in turn holds the underlining data.