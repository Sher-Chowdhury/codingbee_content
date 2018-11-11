---
ID: 472
post_title: NFS and CIFS (remote folders)
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-nfs-and-cifs-remote-folders
published: true
post_date: 2015-10-24 00:00:00
---
In Linux you can share a folder from one Linux machine with another Linux machine, over a network. There are 2 different technologies that allows you to do this, NFS and CIFS. In the next few articles we are going to show you how to use these technology. Along the way we will compare and contrast them as we go, since NFS and CIFS are both designed the same kind of job. .
<h2>Network File System (NFS)</h2>
It is also a useful place to store backups. Because if your machine suffers a massive hdd failure, then the backups have not been lost because they actually reside on another machine.

This article doesn't cover how to <a href="http://codingbee.net/tutorials/rhce/rhce-set-up-an-nfs-server/">setup an NFS server</a>, since that is covered in the RHCE course. Instead we look at how we can connect to an existing shared folder (and make it look like a normal folder on the local machine) that is on a remote NFS server.

NFS has a couple of limitations:
<ul>
	<li>You can't share folders over the internet. NFS can only be used for sharing folders across a local network.</li>
	<li>You can't share folders across platforms. For example you can't share a folder that is on a Microsoft windows machine with a RHEL machine, or vice version.</li>
</ul>
<h2>CIFS (aka Samba)</h2>
NFS has 2 big limitations as outlined above. However CIFS (Common Internet File System) doesn't have these limitation. That means that with CIFS you can share folder across the internet, as well as over a local network. CIFS is also cross platform. This means that you can share a folder that is on a Microsoft windows machine, with a RHEL machine, or vice version.

This makes CIFS a more powerful alternative to NFS.

This article doesn't cover how to <a href="http://codingbee.net/tutorials/rhce/rhce-set-up-a-cifs-server/">Setup a Samba server</a>, since that is covered in the RHCE course. Instead we look at how we can access an existing shared CIFS folder.
<h2>The Scenario</h2>
To help understand how NFS and CIFS work, we’ll refer to a scenario throughout the next few articles to help explain what’s going on. In this scenario we have the 2 Linux machines with the following hostnames:

<strong>The-Server</strong> - This machine has 2 folders available for sharing. The first folder is an "NFS share", and the path to this folder is ...... The second folder is a "CIF share". We’ll refer to this folder as the “Share-Folder”. Aside from these 2 folders, we can’t access this machine in any other way, i.e. we can’t access this machine via ssh, scp, ftp, http,….etc.
<strong>The-Client</strong> - In this scenario we are sitting in front of this machine. In the next few articles we’ll cover how to configure this machine so that it can access The-Server’s "NFS share" and "CIFS share". We can configure The-Client in such a way that the Share-Folder appears as an ordinary folder on the The-Client machine. Therefore to access the Shared-Folder, you simply navigate into it using just the <code>cd</code> command. Any files you create in the Shared-Folder will actually end up being stored on The-Server and not on The-Client.

In the next few articles we will be running a few commands, but these commands will be run on the The-Client machine only.