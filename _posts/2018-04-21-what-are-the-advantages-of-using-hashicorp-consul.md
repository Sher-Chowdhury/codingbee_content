---
ID: 3716
post_title: >
  What are the advantages of using
  Hashicorp Consul?
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/consul/what-are-the-advantages-of-using-hashicorp-consul
published: true
post_date: 2018-04-21 15:47:27
---
Consul is actually a 4 services combined seamless into a single service.  that have been combined into one. These individual services are:

<strong>Service Discovery</strong>: All boxes (mysql servers, apache servers, ntp server, api servers...etc) will have the consul agent deamon running on them which will notify the consul server of it's existence and the type of service it offers. The consul server will register that box under the given service name, of which multiple other boxes can be members of. Then when dns lookup request comes in for a given service then the Consul service will provide the ip address of one of the boxes in the service cluster, in a round robin fashion. 

<strong>Health Checking</strong>: The consul agent will also give information about what health checking should be done on the box to see if it is function.

<strong>KV Store</strong>: Applications can make use of Consul's hierarchical key/value store for any number of purposes, including dynamic configuration, feature flagging, coordination, leader election, and more. The simple HTTP API makes it easy to use.

<strong>Multi Datacenter</strong>: Consul supports multiple datacenters out of the box. This means users of Consul do not have to worry about building additional layers of abstraction to grow to multiple regions. In Consul 'Datacenter' is a logical concept which let's you can separate out your boxes into data centre. Which Datacenter a particular box belongs to is defined in that Consul agent's Consul file. 

What advantages it offers. 

- it can replace get rid of Internal load balancers. The round robin dns system and the health checking features makes Consul a good alternative to Load Balancers
- The KV store is a nice tool (with a gui frontend) that can be used for config management when Alongside <a href="https://github.com/hashicorp/consul-template">Consul Templates</a>. This is a potential alternative to tools like Puppet's hiera.     
- you can define multiple datacenters (similar to environments) and each datacenter has it's own key/value datastore. This is powerful way to store your dev KVs separately from your prod KV store. 
- it does health-checking of nodes in a cluster, and doesn't send traffic to nodes while they are faulty. The fact that the Health Checking Service is deeply linked to the Service, it means that the resulting dns service that Consul provides dynamic+intelligently updates to ensure traffic only gets sent to healthy hosts. 
- health checking is done locally by the consul agents. This means that health checking is distributed and no need to have a pool of monitoring servers (e.g. zabbix servers) to do the health checks. This means that health checking can easily be scaled to thousands of servers without any performance issues. Traditionally health-checking is also done by Load Balancers, but that's no longer required either with this distributed health checking setup. 
- due to the distributed health checking setup. It means that health checks can be performed at a more frequent rate, e.g. once every second! This is something that's not possible with more traditional monitoring software, because of performance issues. 
- any health-check failures are reported using the peer-to-peer gossip protocol amongst it's own cluster. This means that it reduces traffic between the consul server and clients. 
- changes in k/v can result in changes in near real-time. 
- the k/v store is highly available - each consul keeps an uptodate local copy of the entire k/v store. 
- the k/v store can be used to add 
- you can register external services to consul server, e.g. the official aws ntp service.   
- consul and docker works really well together. 
 


Notes:

https://gist.github.com/g0t4/10c82f7ffd8a3f0cbd3980593259b7a8