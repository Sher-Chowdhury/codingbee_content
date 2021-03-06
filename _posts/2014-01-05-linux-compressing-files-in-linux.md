---
ID: 43
post_title: linux-compressing-files-in-linux
author: admin
post_excerpt: ""
layout: post
permalink: http://localhost:8000/?p=43
published: true
post_date: 2014-01-05 00:00:00
---
## File compression.

## Commands covered in this article

gzip      # A file compression utility bzip2      # A file compression utility tar            # A tool that can <l=>archive a whole directory<l=> and convert it into a single file. star #.secure tar. (not as important but worth learning.)

## gzip and bzip

Linux comes pre-installed with a number of file compression utilities. However the 2 most commonly used compression utilities are gzip and bzip2. Both of these tools work pretty much the same way. The only difference is that gzip is slighter better at compressing (resulting in an even smaller file when compared to bzip2) whereas bzip2 is slightly faster at compressing.

<insert code=>

So your choice of whether to use bzip2 or gzip really comes down to what is more important to you, better compression, or faster compression.

To uncompress, you can use the equivalent gunzip and bunzip2 commands:

<insert code=>

See the gzip and bzip2 man pages for more info about these commands.

gzip and bunzip can only compress singles files, and it won't let you compress a directory along with whatever it contains. To do this you need to look at <l=>how to compress a directory using tar<l=>.

## Also see

How to compress a Whole Directory in Linux? (coming soon) 20 practical example on how to use tar (coming soon) Secure your compression using secure tar (star) (coming soon)