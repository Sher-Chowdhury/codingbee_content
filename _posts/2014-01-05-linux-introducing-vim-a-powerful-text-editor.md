---
ID: 51
post_title: 'Linux &#8211; Introducing &#8220;VIM&#8221;, a powerful text editor'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/linux-introducing-vim-a-powerful-text-editor
published: true
post_date: 2014-01-05 00:00:00
---
## Howto: open and edit files in Linux

In Centos, you can view files either from the command line, or using a gui text editor called emacs.

But in this article, we will focus on doing this from the command line.


## View files

In linux there are several commands to view a file. Here are a few of them:

cat {filename}              # Displays the file's content.
less {filename}              # Displays the file's content. this command also has few other options, such as you can also scroll up/down using the arrow keys.
head {filename}              # Displays the first few lines of a file.
tail {filename}              # Displays the last few lines of a file.


# Create and edit files.

There are a lot of ways to create/edit files in linux. One of the ways is by using a based text editor called vim. Vim lets you create/edit files straight from the command line.

vim {filename}       # This opens up the file in the vim editor. If the filename doesn't exist, then it creates a new file by that name.

vim is a powerful text editor which is operated entirely via the keyboard, and doesn't involve using a mouse (apart from the occasional copy&amp;paste). And once you become proficient at vim, you will find that it is quicker to edit files with vim than any other graphical text editor. As a consequence and it has quite a steep learning curve.

The first thing to realise about vim is that it has 3 modes and you have switch between these modes depending on what you want to do. These modes are:

1. Edit mode   - You can access the mode at any time by hittig the Esc key.
2. Write mode - This is the mode for typing content.
3. Command mode - This is a mode that lets you do a number of tasks, including saving changes to your file.

When you open vim, the default mode that you get placed in is the edit mode:

- press ":" to access the command mode.
- press "i" to start (i)nserting text. There are a number of other ways to access the write mode, but this is a good starting point.


Note: you can't directly jump from command mode to write mode, or vice-versa. This means you can only access the command/write mode from the edit mode.

To access the edit mode, simply hit the Esc key. If already in the Esc mode, then hitting Esc again won't do anything.


The best way to learn vim is by going through a pre-installed tutorial:

vimtutor       # A handy step-by-step guide for vim.

vim is something that takes time to get used to. But once you are, working with files becomes really quick and easy.


## Useful tips
1. If you plan to edit a config file, it is always a good idea to make a backup of the file before you make any changes:

cp {config-file} {config-file}-backup

2. As an extra precaution, If editing a config file, always make a duplicate of the line about to be change, then comment out the original line, and then make the edits on the duplicate line. This approach is useful to keep track of the lines you have changed and how the lines looked before.