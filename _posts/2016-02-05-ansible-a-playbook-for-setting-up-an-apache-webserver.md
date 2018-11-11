---
ID: 531
post_title: 'Ansible &#8211; A playbook for setting up an apache webserver'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ansible/ansible-a-playbook-for-setting-up-an-apache-webserver
published: true
post_date: 2016-02-05 00:00:00
---
Here's a simple playbook that sets up an Apache web server on a RHEL/CentOS 7 box:
<pre>
[root@controller playbooks]# pwd
/root/playbooks
[root@controller playbooks]# cat httpd.yaml
---
- name: This sets up an httpd webserver
  hosts: ansibleclient01.local
  tasks:
  - name: Install apache packages 
    yum:
      name: httpd
      state: present
  - name: ensure httpd is running
    service:
      name: httpd 
      state: started
  - name: Open port 80 for http access
    firewalld:
      service: http
      permanent: true
      state: enabled
  - name: Restart the firewalld service to load in the firewall changes
    service: 
      name: firewalld 
      state: restarted
</pre>

Here's a breakdown of what the above yaml syntax shows. At the top level is a single item list. This list item houses a hash with 3 key-value pairs. The first to key's values are of the type string. However the third key's (tasks) houses a list of 4 items. Each one of these list items contains a hash. Each of these hashes is made up of 2 key-value pairs.  

Now let's run this playbook:
<pre>
[root@controller playbooks]# pwd
/root/playbooks
[root@controller playbooks]# ls
httpd.yaml
$ ansible-playbook httpd.yml

</pre>
We can test if this has worked, by opening to http://ansibleclient01.local in your web browser.

&nbsp;