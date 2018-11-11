---
ID: 579
post_title: 'Ansible &#8211; Available setting to add to a playbook&#8217;s header section'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ansible/ansible-available-setting-to-add-to-a-playbooks-header-section
published: true
post_date: 2016-05-06 00:00:00
---
<pre>
- name:  blahblah
  hosts: hostname
  vars:
    var1: value1
    var2: value2
  vars_files:
    - /path/to/var1.yaml
    - /path/to/var2.yaml
  vars_prompt:             this key's value is a 2 item array, each item is a single item hash 
    - name: var_name         
    - prompt: the prompt message itself     # this will do a prompt for more info during runtime 
  sudo: 
  user:
  connection:
  gather_facts:
</pre>

Note: by default behind the scenes the setup module always runs behind the scenes to collect facts. You can disable this behaviour if your playbook doesn't reference any facts. To disable this set gather_facts to 'false', or 'no'.