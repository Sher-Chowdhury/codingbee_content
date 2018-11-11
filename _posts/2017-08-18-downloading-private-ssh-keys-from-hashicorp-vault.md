---
ID: 2323
post_title: >
  Downloading private ssh keys from
  Hashicorp Vault
author: sher
post_excerpt: >
  Hashicorp Vault is commonly used to
  store private ssh keys, e.g. ssh keys
  for a privileged user of an aws ec2
  instances.
layout: post
permalink: >
  https://codingbee.net/tutorials/hashicorp/vault/downloading-private-ssh-keys-from-hashicorp-vault
published: true
post_date: 2017-08-18 09:42:50
---
Hashicorp Vault is commonly used to store private ssh keys, e.g. ssh keys for a privileged user of an aws ec2 instances. This guide walks you through how to pull down an ssh key from Vault and use it to ssh to an aws ec2 instance. 


First you need to set the address of our vault server, therefore run:

<pre>
$ export VAULT_ADDR=https://{vault-address}:{port-number}
</pre>

Then authenticate with vault:

<pre>
$ vault auth -method=ldap username={ldap-username}
Password (will be hidden):
Successfully authenticated! You are now logged in.
The token below is already saved in the session. You do not
need to "vault auth" again with the token.
token: xxxx-xxxx-xxxxx-xxxxx-xxxxx
token_duration: 43199
token_policies: [xxxx xxxx xxxx xxxxx xxxxx]
</pre>

Now you should be able to list secrets:


<pre>
$ vault list secret
xxxx
xxx
xxxx
xxxxxx
</pre>

If a particular secret is a private ssh key, then you can download it like this:



<pre>
$ vault read -field=value secret/ssh-keys/centos.pem > ~/.ssh/id_rsa-centos.pem
$ chmod 600 ~/.ssh/id_rsa-centos.pem
</pre>



Now to start using this ssh key, you need to load it in, like this:

<pre>
$ ssh-add  ~/ssh-keys/id_rsa-centos.pem
Identity added: /Users/schowdhury/.ssh/id_rsa-centos.pem (/Users/schowdhury/.ssh/id_rsa-centos.pem)
</pre>

If the above doesn't work then you might need to do an <a href="https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/#adding-your-ssh-key-to-the-ssh-agent">eval of an ssh-agent</a>. 

now you should be able to ssh into your boxes (assuming the username is 'centos'):


<pre>
$ ssh centos@ec2-instance-fqdn
</pre>