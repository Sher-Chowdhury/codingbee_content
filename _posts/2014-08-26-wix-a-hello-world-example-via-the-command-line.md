---
ID: 145
post_title: 'WIX &#8211; A &#8220;Hello World&#8221; example (via the command line)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/wix/wix-a-hello-world-example-via-the-command-line
published: true
post_date: 2014-08-26 00:00:00
---
Creating an msi, you need 3 things

&nbsp;

&nbsp;

I am now going to create a very simple MSI that will install a software that I'm going to call "<strong>CodingBee</strong>". This MSI will:
<ol>
	<li>Create a folder called "<strong>CodingBee</strong>" which will be under "c:\program files" folder</li>
	<li>Place a single text file in this folder, called <strong>HelloWorld.txt.  </strong>This file only contains 1 line which says "Hello World"</li>
</ol>
&nbsp;

Here's my approach:

&nbsp;
<ol>
	<li>Create a folder called "CodingBeeFolder" in a convienent location (in my case I've created on my deskop)</li>
	<li>Create the <strong>HelloWorld.txt</strong> file and save it inside   the CodingBeeFolder folder.</li>
	<li>Create the wxs file, which I have called CodingBeeXML.wxs, and in the file, I inserted the following xml:</li>
	<li>Open up a powershell terminal and cd into CodingBeeFolder.   At this stage if you view this folder's content, you should see:</li>
	<li>Now, while you are still in the CodingBeeFolder, run the following command:</li>
	<li>Check that running this command has resulted in the "Product.wixobj" file being generated. the wixobj an intermediary file that get's produced as part of the MSI file generating process.</li>
	<li>Run the following (light.exe) command:</li>
	<li>This should have now generated the</li>
</ol>