---
ID: 157
post_title: 'Puppet &#8211; webrick vs passeger web server'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-webrick-vs-passeger-web-server
published: true
post_date: 2014-10-16 00:00:00
---
puppetmaster requires a web server software to run on. By default puppetmaster comes with webrick pre-installed. However you can change this to anything you want. webrick has big drawback that it isn't scalable. 

Puppet enterprise on the other hand is scalable and that is because it comes pre-installed with "passenger" instead of "webrick". Passenger is actually optimised for scalability. 

https://www.google.co.uk/search?q=passenger+web+server&ie=utf-8&oe=utf-8&aq=t&rls=org.mozilla:en-GB:official&client=firefox-a&channel=sb&gfe_rd=cr&ei=7qY_VOC_Ks-q8wfC_oKACw

https://www.phusionpassenger.com/

http://en.wikipedia.org/wiki/WEBrick

https://docs.puppetlabs.com/guides/passenger.html