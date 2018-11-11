---
ID: 151
post_title: 'WIX &#8211; The Installation Sequence'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/wix/wix-the-installation-sequence
published: true
post_date: 2014-08-29 00:00:00
---
there are two tables in the MSI database, that contain the order in which installation events should occur, they are:
<ul>
	<li>InstallUISequence
<a href="http://codingbee.net/wp-content/uploads/2014/08/InstallExecuteSequence.png"><img class="alignnone size-full wp-image-1529" src="http://codingbee.net/wp-content/uploads/2014/08/InstallExecuteSequence.png" alt="InstallExecuteSequence" width="839" height="706" /></a> <a href="http://codingbee.net/wp-content/uploads/2014/08/InstallUISequence.png"><img class="alignnone size-full wp-image-1530" src="http://codingbee.net/wp-content/uploads/2014/08/InstallUISequence.png" alt="InstallUISequence" width="736" height="705" /></a></li>
	<li>InstallExecuteSequence
<a href="http://codingbee.net/wp-content/uploads/2014/08/InstallExecuteSequence.png"><img class="alignnone size-full wp-image-1529" src="http://codingbee.net/wp-content/uploads/2014/08/InstallExecuteSequence.png" alt="InstallExecuteSequence" width="839" height="706" /></a></li>
</ul>
&nbsp;
<h2>InstallUISequence</h2>
This covers the first half of the install process. during this period there are no acual changes applied to the machine. Instead it is the msi ask for input from the user, and checks whether the machine meets it's pre-requisites (e.g. correct version of .net installed)

there are a number of standard actions (i.e. non-custom-actions) that can be added to this table

&nbsp;
<ul>
	<li>FindRelatedProducts</li>
	<li>AppSearch</li>
	<li>LaunchConditions</li>
	<li>ValidateProductID</li>
	<li>CostInitialize</li>
	<li>FileCost</li>
	<li>CostFinalize</li>
	<li>MigrateFeatureStates</li>
	<li>ExecuteAction</li>
</ul>
this side is run under the msi initiating user's privelege (aka client side)

&nbsp;
<h2>InstallExecuteSequence</h2>
This is the 2-half ot the install process and happens after   InstallUISequence has completed. This is where the actual install happens and includes tasks like:
<ul>
	<li>copying files to target directories</li>
	<li>updating the registry</li>
	<li>add a new entry to "Program and Features"</li>
</ul>
This sequence is run using the localsystem user's privilege (aka server side). Here are the standard actions that takes place here:
<ul>
	<li>InstallValidate - double checks that there is enough disk space, and relevant files have not been locked by running processes</li>
	<li>InstallInitialize   - a bit like taking a snapshot, so that rollbacks can happen easily. This prevents any partial installs.</li>
	<li>ProcessComponents</li>
	<li>UnpublishFeatures - only occurs when uninstalling an msi. It is ignored during the install.</li>
	<li>RemoveRegistryValues - only occurs when uninstalling an msi. It is ignored during the install.</li>
	<li>RemoveShortcuts - only occurs when uninstalling an msi. It is ignored during the install.</li>
	<li>RemoveFiles - only occurs when uninstalling an msi. It is ignored during the install.</li>
	<li>InstallFiles -</li>
	<li>CreateShortcuts</li>
	<li>WriteRegistryValues</li>
	<li>RegisterUser</li>
	<li>RegisterProduct - registers your product with Programs and Features and stores a copy of the MSI package in the Windows Installer Cache, found at %WINDIR%\Installer.</li>
	<li>PublishFeatures</li>
	<li>PublishProduct</li>
	<li>InstallFinalize</li>
</ul>
(note , it might also do the actions of the first-half, if for any reason they failed to happen during the 1st half)