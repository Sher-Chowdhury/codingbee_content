---
ID: 107
post_title: 'PowerShell &#8211; Common Paramaters'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-common-paramaters
published: true
post_date: 2014-06-01 00:00:00
---
Every command in powershell, has a set of "common parameters" (i.e. options). This is indicated in all the help pages, in the syntax section...which alway ends with:

[]

This family of paramaters is made up of the following parameters:
- Debug (db)
- ErrorAction(ea)
- ErrorVariable(ev)
- OutVariable(ov)
- OutBuffer(ob)
- Verbose(vb)
- WarningAction(wa)
- WarningVariable(wv)

For more info, check out:

help about_CommonParameters # "help" is a function of get-help, and does the same thing.