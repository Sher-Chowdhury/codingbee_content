---
ID: 240
post_title: 'Puppet &#8211; Organising your puppet code into separate files'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-organising-your-puppet-code-into-separate-files
published: true
post_date: 2014-12-24 00:00:00
---
So far we have looked at how to organise your code by compartmentalizing your resources into classes.

However, your site.pp can still get really big as your write more and more classes. That's why the next logical step is to store your classes in separate manifests, and then "dot source" them. In Puppet there are three ways to do this:

<ul>
	<li>Using the <a href="http://codingbee.net/tutorials/puppet/puppet-using-import-keyword/" title="Puppet - Using the “import” keyword">import</a> keyword</li>
	<li>The "<a href="http://codingbee.net/tutorials/puppet/puppet-puppet-apply-approach/" title="Puppet - The “puppet apply” approach">apply puppet</a>" method</li>
	<li><a href="http://codingbee.net/tutorials/puppet/puppet-modules/" title="Puppet - Modules">Modules</a> - this is the best approach</li>
</ul>

We'll now look at each approach in turn.