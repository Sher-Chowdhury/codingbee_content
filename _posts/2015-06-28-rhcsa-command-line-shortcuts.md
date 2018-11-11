---
ID: 437
post_title: 'RHCSA &#8211; Command Line shortcuts'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-command-line-shortcuts
published: true
post_date: 2015-06-28 00:00:00
---
no quiz required

Here are some handy tips and tricks you can perform inside the bash terminal to help you speed up productivity


<h2>Retrieve and run an earlier command</h2>

This is done in few ways:

<ul>
	<li>By pressing up and down to scroll through previously run commands</li>
	<li>Hold down <code>ctrl+r</code>, this will bring up the "reverse-i-search" prompt. You can then type a part of the command, and this prompt will automatically return the most recent match from your command history, You can scroll back to earlier matches by doing <code>ctrl+r</code> repeatedly</li>
	<li>Using the "!{command}" notation. this will run the last matching command what ever that was. This is particurly handy for running long unwieldy commands, e.g. "!scp"</li>
        <li>Use <code>history</code> command - this will output a list of your previously run commands. You can then run a command by copy+pasting one of your historical commands. Or you can run !{history number}</li>
</ul>



<h2>Command editing shortcuts</h2>



While on the command line you can do the following:

<ul>
	<li><code>Ctrl+a</code> (or the <code>home</code> key ) : jump to the line's beginning</li>
	<li><code>Ctrl+e</code> (or the <code>end</code> key) : Jump to end of line</li>
	<li><code>Ctrl+u</code> : delete everything from start of line to cursor</li>
	<li><code>Ctrl+k</code> : delete everything from the cursor to end up line</li>
	<li><code>Ctrl+LeftArrow</code> : go to the next word on the command line</li>
	<li><code>Ctrl+RightArrow</code> : go to the previous word on the command line </li>
	<li>Press <code>tab</code> key twice midway through writing a command, this will try to autocomplete your command.</li>

</ul>