---
ID: 7
post_title: 'Linux &#8211; Help at the Command Line'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/redhat/linux-help-at-the-command-line
published: true
post_date: 2014-05-04 00:00:00
---
<strong>Commands:</strong>

man {command}

info {command}

pinfo {command}                # displays man pages in a slight differently user friendly format.

man -k {search term}       # Searches all the search terms for the search-term, and lists all the man pages where there is a match.

apropros {search term} # same as the “man -k {search term}”

type {command}               # tells you if command is builtin or not, if not then shows the path.

which {command}                # shows command’s file location (even for builtin ones).

whatis {command}             # one line description of a command.

{command} --help            # quick tips on how to use a command.

{command} -h                 # use this if the “--help” doesn’t work.

man hier                             # Explains what’s stored in the various directories in RHEL (layout of filesystems).

<strong>Config files:</strong>



<pre>

$ cd /usr/share/doc        # directory containing all the man pages. This can also 
                        # contain diagram/pictures/graphics (you need to open 
                        # it via Firefox to view them). You can also find lots of 
                        # sample config files here. This is very useful!!!

$ cd /usr/share/info       # directory containing all the info pages.

</pre>


<strong>Notes:</strong>

When you install a new software, the man pages may not be immediately added to the man pages database. To manually refresh this DB, run the following command:

/etc/cron.daily/makewhatis.cron

Each man page is made up of sections (sections 1-9). For each command, some of these (if not most) sections are not applicable and hence empty. To see which sections are available for a given command, do:

whatis {command} # e.g. try “whatis passwd”, or “whatis mount”

The whatis command also lists what help sections are available and then you can access different sections like this:

<pre>man {section number} {command}</pre>

e.g. try:

<pre>man 5 passwd                                  #This will display the help pages for the /etc/passwd config file.
</pre>

Note the man command quiries documentation stored in /usr/share/man

Some software packages don’t come with man pages. Instead, the man pages have to be installed separately (not sure how to identify these packages, might be possible using yum or rpm)

&nbsp;

<strong>Related Services:</strong>

Inserttexthere

<strong>Must survive reboot:</strong>

/etc/cron.daily/makewhatis.cron     #updates the man page’s db manually.

<strong>Software to install:</strong>

n/a

<strong>GUI tool:</strong>

system --=> help

yelp #start gui help from the command line interface.

<strong>Book ref:</strong>

page 28, chapter 3

<strong>Study guide ref:</strong>

RH124 - page 122 (this has a table showing listing all the available sections and describing it)

RH124 - page 21 (getting help via gnome)

<strong>Need to learn more about:</strong>

<a href="http://en.wikipedia.org/wiki/Man_page">http://en.wikipedia.org/wiki/Man_page</a>

<a href="http://www.freebsd.org/cgi/man.cgi?query=hier&amp;apropos=0&amp;sektion=0&amp;manpath=FreeBSD+6.1-RELEASE&amp;format=html">man page for “hier</a>”

&nbsp;