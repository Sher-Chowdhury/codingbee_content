---
ID: 302
post_title: 'PostgreSQL &#8211; Automate PostgreSQL installation/setup using Puppet'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/postgresql/postgresql-automate-postgresql-installationsetup-using-puppet
published: true
post_date: 2015-02-23 00:00:00
---
This guide will walk you through how to use puppet to automate the steps we took manually when we followed the <a href="http://codingbee.net/tutorials/postgresql/postgresql-install-postgresql-and-then-create-a-db-and-user-account/" title="PostgreSQL - Install PostgreSQL, and then create a DB and User Account">postgreSQL installation and setup guide</a>.


based on the manual guide, we need to write puppet code to:

1. Install the correct version of postgres, in my case that is postgreSQL 9.3.
2. Create a new DB. 
3. Create a user. 
4. Assign user full access privileges to the db
5. enable remote access. 

All of the above can be achieved with the puppetlab's <a href="https://forge.puppetlabs.com/puppetlabs/postgresql">postgresql puppet module</a> and the following node definition in the site.pp file:


<pre>

node puppetagent01 {

  # This sets some of the defaults..
  class {'postgresql::globals':
    version => '9.3',
    manage_package_repo => true,
  }
  ->

  # This installs the postgres software for the version specified above..
  class { 'postgresql::server':.
    # ip_mask_allow_all_users    => '0.0.0.0/0',
    listen_addresses           => '*',
  }
  ->
  # This creates a new db called "reviewdb" and at the same time also creates a new db account called "gerritdbuser".
  # which is automatically granted full access to the new db.
  postgresql::server::db { 'reviewdb':
    user     => 'gerritdbuser',
    password => postgresql_password('gerritdbuser', 'admin123'),
  }

  postgresql::server::pg_hba_rule { 'allow application network to access app database':
    description => "Open up postgresql for access from 0.0.0.0/0",
    type => 'host',
    database => 'reviewdb',
    user => 'gerritdbuser',
    address => '0.0.0.0/0',
    auth_method => 'trust',
  }


}

</pre>



 
See also:

http://blog.2ndquadrant.com/devops-testing-postgresql-with-vagrant-on-your-computer-part-two/