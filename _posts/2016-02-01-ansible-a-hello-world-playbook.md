---
ID: 527
post_title: 'Ansible &#8211; A &#8220;hello world&#8221; Playbook'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ansible/ansible-a-hello-world-playbook
published: true
post_date: 2016-02-01 00:00:00
---
Playbooks are written in yaml format, and you can actually choose where to store your playbooks. In my case I'll create a folder called "playbooks" for storing my playbooks, and I'll create this in the root user's home directory:

&nbsp;
<pre>[root@controller ~]# pwd
/root
[root@controller ~]# mkdir playbooks
[root@controller ~]# cd playbooks
[root@controller playbooks]# pwd
/root/playbooks</pre>
In this directory, I'll then create the following file:

&nbsp;
<pre>[root@controller playbooks]# cat HelloWorld.yml
---
- name: This is a hello-world example
  hosts: ansibleclient01.local
  tasks:
  - name: Create a file called '/tmp/testfile.txt' with the content 'hello world'.
    copy: 
      content: "hello world\n" 
      dest: /tmp/testfile.txt
</pre>
This playbook is designed to create the file "/tmp/testfile.txt" on the client ansibleclient01.local using ansible's <a href="http://docs.ansible.com/ansible/copy_module.html" rel="nofollow">copy module</a>.

&nbsp;

You can think of this playbook as a script that you can execute. You can execute this playbook using the "ansible-playbook" command:

&nbsp;
<pre>[root@controller playbooks]# ansible-playbook HelloWorld.yml

PLAY [This is a hello-world example] *******************************************

TASK [setup] *******************************************************************
ok: [ansibleclient01.local]

TASK [Create a file called '/tmp/testfile.txt' with the content 'hello world'.]
changed: [ansibleclient01.local]

PLAY RECAP *********************************************************************
ansibleclient01.local : ok=2 changed=1 unreachable=0 failed=0

</pre>
This results in the following file being created on the client:

&nbsp;
<pre>[root@ansibleclient01 /]# cat /tmp/testfile.txt
hello world</pre>
&nbsp;

Now let's delete the testfile.txt. Then return back to the controller.

&nbsp;

Now let's create testfile.txt in a different way, this time using a static file. we do this by first creating a static file and placing it in a logical location. In my case I created a folder called "files" and placed the testfile.txt in it:

&nbsp;
<pre>[root@controller playbooks]# tree
.
├── files
│     └── testfile.txt
└── HelloWorld.yml</pre>
<pre>1 directory, 2 files
[root@controller playbooks]# cat files/testfile.txt
hello world</pre>
&nbsp;

I then replaced the copy module's "content" attribute to "src":

&nbsp;
<pre>[root@controller playbooks]# cat HelloWorld.yml
---
- name: This is a hello-world example
 hosts: ansibleclient01.local
 tasks:
 - name: Create a file called '/tmp/testfile.txt' with the content 'hello world'.
   copy: 
     src: ./files/testfile.txt 
     dest: /tmp/testfile.txt</pre>
&nbsp;

Notice, I think the src path is relative to where the playbook's location. You can use absolute path but that will make it inflexible to move the static file to another location.

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

http://docs.ansible.com/ansible/playbooks_intro.html

&nbsp;

&nbsp;