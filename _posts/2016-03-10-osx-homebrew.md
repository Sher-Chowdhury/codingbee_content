---
ID: 552
post_title: 'OSX &#8211;  homebrew'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/osx/osx-homebrew
published: true
post_date: 2016-03-10 00:00:00
---
mac’s version of yum is called ‘brew’:
http://brew.sh/


Once installed here’s an analogy:


<ul>
	<li>Yum     =  brew</li>
	<li>Package  = formula</li>
	<li>Repo = tap</li>
	<li>Yum repolist = brew tap</li>
</ul>



The 'epel-release' equivalent in mac is called cask


You can find out how to use cask here, see:
<a href="https://caskroom.github.io/" rel="nofollow">https://caskroom.github.io/</a>


Here are some of my favourite formulas (aka packages):



<pre>

$ brew list
ansible
packer
shellcheck
autoconf
doctl - digitalocean cli
sqlite
dos2unix
go		
pwgen		
terraform
bash-completion
jq
mtr - traceroute and ping in a single tool.
python		
tflint
bfg		
tree


$ brew cask list
atom                 
firefox              
iterm2              
java                 
karabiner            
vagrant              
virtualbox




</pre>