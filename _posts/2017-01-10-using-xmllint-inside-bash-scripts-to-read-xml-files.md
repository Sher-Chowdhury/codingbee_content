---
ID: 594
post_title: >
  Using xmllint inside bash scripts to
  read xml files
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/using-xmllint-inside-bash-scripts-to-read-xml-files
published: true
post_date: 2017-01-10 00:00:00
---
xmllint --xpath "string(//channel/item/link)" test.xml

xmllint --xpath "string(//channel/item/*[name()='content:encoded'])" test.xml