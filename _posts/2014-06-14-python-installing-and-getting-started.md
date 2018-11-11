---
ID: 114
post_title: 'Python &#8211; Installing and Getting Started'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/python/python-installing-and-getting-started
published: true
post_date: 2014-06-14 00:00:00
---
There are 2 versions of python v2.x.x and version v3.x.x.x

v3 is actually a complete rewrite of v2 and will eventually replace v2. v2 is therefore legacy and you should avoid using it if possible.

&nbsp;

&nbsp;

Most linux distro comes with python as standard.

Python works example the same way on all linux and windows platforms.

To install python on windows, you need to download the appropriate msi installer which you can find on the  <a href="https://www.python.org/downloads/">official python website</a> website. Download the 6b-bit msi windows installer if you have a 64-bit windows OS.

during the install process, it will create a new python folder directly under the c:\ rather than in the program folders, this is so that python can circumvent some windows based limitations.

Make a note of where python will be installed, you need to check that this folder exists after the install completes, and within this folder you should find a "Scripts", for example you should have folders like:

&nbsp;

C:\Python34

C:\Python34\Scripts

One you have made a note of these two folders, you need to add them to your machine's environment variables (using the gui). You go to:

Control Panel | System | Advance systems settings | advanced (tab) | Environment Variable (button)

Here select the path variable, and append the above two folder paths with semicolon.

After that you should be able to open powershell and type "python" to enter the python terminal....which displays three arrows.

Useful links:

&nbsp;

The standard library - https://docs.python.org/3/library/

&nbsp;

&nbsp;