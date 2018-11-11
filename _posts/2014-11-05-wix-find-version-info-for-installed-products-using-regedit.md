---
ID: 171
post_title: 'WIX &#8211; Find version info for installed products using regedit'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/wix/wix-find-version-info-for-installed-products-using-regedit
published: true
post_date: 2014-11-05 00:00:00
---
open up regedit, and then navigate to:

HKEY_LOCAL_MACHINE/SOFTWARE/Microsoft/Windows/CurrentVersion/Installer/UserData/Products

Under this folder you will find a list of GUIDs, of which only of them is the one you want. Therefore:

While you have "Products" select, got to edit->find

Then type the name of the software you want to find more info about. 

This will open up th corresponding guid that you are interested in.