---
ID: 317
post_title: 'Git &#8211; The .gitignore'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/git/git-the-gitignore
published: true
post_date: 2015-03-09 00:00:00
---
There are some untracked files in your project file that you want git to completely ignore, e.g. temporary files, log files, etc. You can do this by creating a special new file called .gitignore which sits alongside the .git folder. this file can list everything you want ignored, here's an example of one:

<pre>
[vagrant@localhost puppetlabs-ntp]$ ls -la | grep git
drwxrwxr-x 8 vagrant vagrant  4096 Mar  9 15:46 .git
-rw-rw-r-- 1 vagrant vagrant    83 Mar  9 14:02 .gitignore
[vagrant@localhost puppetlabs-ntp]$ cat .gitignore
pkg/
Gemfile.lock
vendor/
spec/fixtures/
.vagrant/
.bundle/
coverage/
.idea/
*.iml
[vagrant@localhost puppetlabs-ntp]$
</pre>


Tip: if you are using eclipse, then you may want to add ".project" in the above file too.