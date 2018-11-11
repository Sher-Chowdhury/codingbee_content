---
ID: 508
post_title: 'AWS &#8211; Route 53 (DNS) overview'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/aws/aws-csa-associate/aws-route-53-dns-overview
published: true
post_date: 2016-01-16 00:00:00
---
<h3>General DNS fundamentals:</h3>
&nbsp;

<strong>Time to Live TTL</strong> - Global dns servers and desktop pcs caches dns resolutions results. The default is 2 days. So if you want to migrate to new a new server, then the best thing to do is first reduce TTL to 300 seconds (5 mins), then wait about 4 days for the new caching to propagate through. Then update entry in route 53. This will reduce intermittent issues, and people will start using the new servers more quickly.

<strong>Top Level Domain Names</strong> - All Domain names are organised into top level domain names, e.g. .com, .net, .uk,....etc. All these Top Level Domain names are controlled by a central organisation called <a href="http://www.iana.org/" rel="nofollow">Iana</a>. They have a  <a href="http://www.iana.org/domains/root/db" rel="nofollow">full list of top level domain names</a>. There are Top Level Domain Servers (aka a type of dns server) around the world that redirects traffic to a lower level DNS server.  Note: the "co.uk" subdomain is owned by a company called <a href="http://www.nominet.uk/domains/our-domains/uk-domains/" rel="nofollow">nominet</a>, according to the <a href="http://whois.domaintools.com/co.uk" rel="nofollow">whois entry</a>.

<strong>Domain Registrars</strong>: These are companies that have the authority to register subdomains under a top level domains. E.g. godaddy is one of the biggest companies that does this. Domain Registrars registers a subdomain on behalf of it's clients, via  a service called <strong>InterNIC</strong>. This service, InterNIC, registers a subdomain, as long as it is unique.

<strong>WhoI</strong><strong>S Database</strong>: This is a central Database that the InterNIC service registers new (successfully processed) subdomains to.

<strong>NS Records</strong>: These  is a record that is stored in dns servers, and is made up 2 "Nameserver" urls. It is used by dns servers to forward dns resolution requests to, if it can resolve it itself.

<strong>CNAME</strong>: This a record stored on dns servers that simply is used to resolve one domain name to another. E.g. create a cname record that resolves codingbee.co.uk to codingbee.net

<strong>Alias Records</strong>: This is something that's specific to AWS.  It is similar to CNAME, however when creating one in route53, if you selected alias from dropdown when creating the record, then the actual field becomes a dropown list of the following:
<ul>
 	<li><strong>ELB's dns address</strong>  - this is useful because ELBs don't have a static ip address, it is always changing, and there are several. You can check this by running a dig command to an ec2 instance that sits behind an ELB.</li>
 	<li>Cloudfront Distributions</li>
 	<li>S3 buckets</li>
</ul>
The main reason that Alias Records exists is so to be able to link up the domain name to an ELB, since ELBs can have several constantly changing IP addresses, due to the nature of how ELBs work internally. This also means that an ELB's ip addresses are never visible from the AWS web console.

One big difference between CNAME and ALIAS records is that it's free to use ALIAS, but you will be charged for each CNAME entry. So always using ALIAS records wherever possible.

&nbsp;

<strong>Here's an example</strong>:

E.g. if a Top level domain server get's a resolution request for the url "codingbee.co.uk", then it will forward this request to a ".uk" dns server, which in turn will forward it onto a "co.uk" dns server, which will get forwarded to a nominet owned server. My domain register, "domainmonster" has it's nameservers registered with nominet, so the nominet server will forward the request to a domain monster server. My webhost provider (justhostme) provided me with there 2 nameserver urls, which I have provided to domainmonster. Hence domainmonster will then resolve the url request to this nameserver.

&nbsp;

<strong>Key points</strong>

When you create a new "Hosted Zone", in route 53, you will find after it is created, it has 2 entries by default, first one is an NS record, and the second one is an SOA record. The NS usually comes with 4 domain addresses by default, for even higher redundancies. You can copy and paste this into your corresponding entry in your domain register's online portal, e.g. godaddy or domainmonster.

&nbsp;

After that's done, the domain registrar's dns servers will start forwarding dns resolution requests to route 53.

&nbsp;
<h3>Route 53 intro</h3>
&nbsp;

AWS primarily uses IPV4, and IPV6 isn't properly supported yet.

route 53 acts as an internal+external DNS. It is commonly used with ELB to route traffic requested from a dommain, to the ELB.

Route 53 provides 2 nameserver values that you can input into a domain registrar (e.g. domainmonster.com).

Route 53 is the name of the dns solution provided by AWS.

Route 53 also lets you register a domain (i.e. no longer need to use domainmonster.com)

Also you can transfer domains to aws.

Hence in summary:

&nbsp;

route 53 offers:
<ul>
 	<li>dns server - resolve urls to ip address</li>
 	<li>register domains</li>
 	<li>transfer in domains</li>
 	<li>latency, GEO, basic, and failover routing policies - these maximises performance and high-availability</li>
 	<li>failover to S3 or Cloudfront -   this serves static content until vpc is back up again. It could deliver static cached data (partial service), or "website is currently down we will be back soon" (no service)</li>
</ul>
&nbsp;