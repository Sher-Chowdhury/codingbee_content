---
ID: 269
post_title: 'Puppet &#8211; Hiera lookup functions'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-hiera-lookup-functions
published: true
post_date: 2015-01-19 00:00:00
---
By default, puppet only submits a hiera query when it is trying to find a value for a class parameter. OUtside of that, hiera doesn't get used by puppet. As for your manifests, you will mainly be using data available from facter and puppet built-in variables. 


However if there is certain data that you want to use in your manifests, but are stored in yaml files. Then you can instruct puppet to perform a hiera lookup using a number of functions:

<ul>
	<li><a href="https://docs.puppetlabs.com/references/latest/function.html#hiera">hiera</a></li>
	<li><a href="https://docs.puppetlabs.com/references/latest/function.html#hieraarray">hiera_array</a></li>
	<li><a href="https://docs.puppetlabs.com/references/latest/function.html#hierahash">hiera_hash</a></li>
	
</ul>

 
There is a fourth a hiera related function, which is called "<a href="https://docs.puppetlabs.com/references/latest/function.html#hierainclude">hiera_include</a>". <a href="https://docs.puppetlabs.com/hiera/1/puppet.html#assigning-classes-to-nodes-with-hiera-hierainclude">Hiera include can assign classes to nodes</a>.  Will cover more about that later. 

https://docs.puppetlabs.com/hiera/1/puppet.html#hiera-lookup-functions