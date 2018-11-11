---
ID: 54
post_title: 'Linux &#8211; Accessing the linux terminal'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/linux-accessing-the-linux-terminal
published: true
post_date: 2014-01-05 00:00:00
---
The way you access the   linux command line terminal, depends on the scenario:

Scenario 1 - Access your Linux desktop machine's terminal

Using the gnome ui, interface, simply go to:

Useful Tip: you can create a desktop shortcut to save time.

Scenario 2 - Access a remote linux server's   terminal from a windows desktop machine.

In most corporate workplaces, linux servers are locked away in server rooms. In these cases, you can access the server's terminal remotely by using a software called putty. Putty creates a connection to the linux server and opens up a virtual terminal. After that you can use this terminal just as if you were access the linux server directly.

Useful Tip: In a corporate workplace, you may need to remotely connect to several remote linux machines. Then you might want to use a putty-addon called putty connection manager (PCM).   The cool thing thing about pcm is that it can be:
<ul>
	<li>used to store all your linux login credential....so that you don't have to have keep them all memorised.</li>
	<li>used automatically log in to a linux machine, so no need to repetitively type in the username and password</li>
	<li>lets you open up several terminals in a tabbed interface....so that you can work on multiple linux machines simultaneously</li>
</ul>
Scenario 3 -   Access a remote linux server's   terminal from a linux desktop machine

If your local machine is a linux machine, then all you need to do is open a terminal (as described in scenario 1), and then use the ssh command to establish a remote linux server connection. After that you can manage the remote server as if you are accessing it directly.

Note: This scenario isn't very common because most desktop machines in a corporate workplace run on windows (rather than linux), and only the servers run on linux.

[vision_content_box style="autumn" title="Content box title"] Awesome content goes here. [/vision_content_box]

Scenario 4 (more advanced)- Create a connection between 2 remote linux servers

However ssh is often used in shell scripting, and when you want to remotely connect to a linux server from another linux server.