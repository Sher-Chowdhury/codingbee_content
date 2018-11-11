---
ID: 351
post_title: 'RHCSA &#8211; LDAP Overview'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rhcsa/rhcsa-ldap-overview
published: true
post_date: 2015-04-09 00:00:00
---
<h2>Basic LDAP concepts</h2>
In big companies, it's best to store all employees login credentials centrally on a single server. This means that you can maintain them in a single place rather than lots of places. This central server is called an "LDAP server". LDAP is short for <strong>Lightweight Directory Access Protocol</strong>. There is a Microsoft alternative to LDAP, which works in a similar way to LDAP and is called "active directory".

<h3>What data does an ldap server store?</h3>
LDAP is used to store more than just usernames and passwords. It stores all kinds of information for a given user account, e.g.:

<ul>
	<li>username</li>
	<li>password</li>
	<li>job title</li>
	<li>first name</li>
	<li>surename</li>
	<li>email</li>
	<li>phone number</li>
	<li>group names</li>
	<li>...etc</li>
</ul>


However an ldap server isn't limited to just storing user account data, It can also store data relating to:

<ul>
	<li>servers</li>
	<li>printers</li>
	<li>profiles</li>
</ul>

<h3>How is data stored in an LDAP server?</h3>
LDAP organises all the data in a hierarchical structure. The linux directory tree structure is a good analogy to this. This means that you can can refer to a particular resource (e.g. user account) by referencing all the string of "nodes" that drills down to that given resource account. 

LDAP actually uses the ldap server's own fqdn to build the hierarchy's core tree structure. For example if the ldap server's fqdn is:

<pre>users.codingbee.co.uk</pre>

Based on this fqdn, in ldap terms we have:

 <ol>
	<li>the top level node is "uk". In LDAP, this node is referred to as a dc (domain component).</li>
 	<li>the next node is "co", which is referred to as a dc (domain component).</li>
	<li>then we have another dc called "codingbee"</li>
 	<li>Then we have the final, lowest-level node, "users". In LDAP this node is referred to as an "ou" (Organisational Unit). The ou is actually an extension that ldap has added onto the fqdn like tree.</li>
</ol>

There can be other ou's available, e.g.:

<pre>
- servers.codingbee.co.uk
- printers.codingbee.co.uk
</pre>

Note, these fqdns which are prefixed with the ou, don't actually exist. they are just used to help you understand what's happening. These ou's are the lowest level of the hierarchy. The resource data is stored under inside the ou. 

Now let's say that we have a user account called "david", then by following this convention, david's user account data should be located at:

<pre>david.users.codingbee.co.uk</pre>

Here this "david" prefix is referred to as a "cn" (common name). Once again this string doesn't actually exist, it is just being used to help you understand what's happening.

When the ldap client requests david's user account data, from the ldap server, it does so by sending the request in the following form:


<pre>
cn=david,ou=users,dc=codingbee,dc=co,dc=uk  
</pre>

This is the ldap specific syntax to represent a particular record, which in this case is the record of "david". In LDAP a record, e.g. "david" is referred to as an "entry". The above reference uniquely identifies an entry, and in LDAP this is referred to as "Distinguished Name", aka "dn", and the full ldap syntax is:

<pre>
dn: cn=david,ou=users,dc=codingbee,dc=co,dc=uk  
</pre>



Each entry can be thought of as a hash made up of key-value pairs. However in ldap, a key is referred to as an <strong>attribute type</strong> and a value is referred to as <strong>attribute value</strong>. An attribute for the user account "david", could be:

<pre>   
job title = accountant
</pre>



In big companies it is common to have a central openldap server that all the other Linux servers to authenticate user login credentials against. This kind of setup is known as "single sign-on", aka SSO.

For the RHCSA and RHCE exam, you only need to know how to set up your Linux machine as an LDAP client that can connect to an existing LDAP server. You don't need to know how to setup the LDAP server itself.