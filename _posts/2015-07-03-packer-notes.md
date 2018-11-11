---
ID: 438
post_title: 'Packer &#8211; notes'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/packer-notes
published: true
post_date: 2015-07-03 00:00:00
---
Note, when you want to create a box to be shared on atlas, you need to ensure yoru box has the following configurations:

http://docs.vagrantup.com/v2/boxes/base.html  (e.g. root user's password should be "vagrant")





https://atlas.hashicorp.com/help

https://atlas.hashicorp.com/help/vagrant/boxes/create
https://atlas.hashicorp.com/CodingBee

Install Packer on windows:

https://www.packer.io/downloads.html

this is a binary, so simply unzip it and place it a in a place like:


C:\HashiCorp\Packer


Then update windows "PATH" environment variable

Then open powershell termianl and run "packer --help".  


Now create your packer projects folder, e.g. :

C:\packer

than download an iso into it e.g. the centos dvd iso:

C:\packer


While it's downloading, create an empty json file, give it a meaningful name:

centos-dvd-iso-virtualbox.json



Enter the following in the json file:

<pre>
{
  "builders": [
    {
      "type": "virtualbox-iso",
      "guest_os_type": "RedHat_64",
      "iso_url": "CentOS-7-x86_64-DVD-1503-01.iso",
      "iso_checksum": "99E450FB1B22D2E528757653FCBF5FDC",
      "iso_checksum_type": "md5",
      "ssh_username": "packer",
      "ssh_password": "packer",
      "ssh_wait_timeout": "30s",
      "shutdown_command": "echo 'packer' | sudo -S shutdown -P now"
    }
  ],

  "provisioners": [
    {
      "type": "shell",
      "script": "setup_things.sh"
    }
  ]
}
</pre>

I created the above from the sample in:

https://www.packer.io/docs/builders/virtualbox-iso.html

You also need to find a checksum value which you can do in powershell v4 using:

<pre>
Get-FileHash c:\path\to\file -Algorithm MD5</pre>


Then in your powershell terminal, cd into this directory, and run:



<pre>
$ packer validate centos7-dvd-iso-virtualbox.json
</pre>


It will fail citing that setup_things.sh scripts doesn't exist, create an empty file in the cwd and try again.

Next do:

<pre>
$ packer build centos7-dvd-iso-virtualbox.json
</pre>


Need to use convert to vagrant:

https://www.packer.io/intro/getting-started/vagrant.html


Now upload your box to hashicorp-atlas:

https://atlas.hashicorp.com/




http://digitalsandwich.com/packer-built-centos-vagrant-base-box-automated-build/