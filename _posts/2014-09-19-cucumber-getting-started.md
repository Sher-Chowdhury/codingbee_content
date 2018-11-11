---
ID: 155
post_title: 'Cucumber &#8211; getting started'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/cucumber-getting-started
published: true
post_date: 2014-09-19 00:00:00
---
Cucumber runs on top of "ruby on rails" (ror). To install ror, go to:

http://railsinstaller.org/en

Now set the path variable:
in cmd you do:
path=%path%;C:\RailsInstaller\Ruby1.9.3;C:\RailsInstaller\Ruby1.9.3\bin

in powershell you do:
$env:path="$env:path;C:\RailsInstaller\Ruby1.9.3;C:\RailsInstaller\Ruby1.9.3\bin"


Now we create a folder to house all our rails projects e.g.:

mkdir RailsProjects

Now within this directory you create your first project:

PS C:\Users\Mir\RailsProjects> rails new cucumberproject1

This will create a new folder by this name and within this folder it will contain the project files. 

next, cd into the project and install the relevent gems using the gemfile.conf file, insert the following at the bottom of the file:

<pre>
group :CucumberGems do
	gem 'cucumber-rails'
	gem 'database_cleaner'
end
</pre>

Next, while in the project folder, run:

install bundler    


this will read the gemfile and download the specified gems. note you need internet connection for this because it downloads gems from the internet and installs them. 


you can also do:

update bundler 

if you made a change to gemfile and want to do an update. 


Next you need to install cucumber, while in the project folder, do:

rails generate cucumber:install

this command will create a new "features" folder, this is where all your cucumber files goes in.