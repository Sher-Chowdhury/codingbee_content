---
ID: 2202
post_title: >
  Data Packets, packet headers, and the
  OSI model
author: sher
post_excerpt: >
  A public IP address allows a machine to
  be accessible from the internet, whereas
  a private IP address allows machine to
  be accessible by other machines that are
  also attached to the same network.
layout: post
permalink: >
  https://codingbee.net/tutorials/linux/networking/data-packets-and-the-osi-model
published: true
post_date: 2017-07-26 22:55:19
---
When 2 devices wants to communicate with each other over a network, it does so by constructing packets of data and then sending them to each other. A data packet is basically a structured data (similar to <a href="https://www.w3schools.com/xml/xml_whatis.asp" rel="nofollow">xml</a>), where the actual payload data is nested inside header tags. The header tag's data contains information to help routing the data packets. These header tags are nested inside other header tags, which in turn are nested inside other header tags.

You can imagine a data packet being similar to a hand written letter (data payload) that is put inside an envelope and an address written on that envelope (packet header, along with it's information), which in turn is put inside a bigger envelope with another address written on it (another header)....and so on.

A typical scenario that would generate these data packets, is when a person opens up firefox on their laptop and visits google.com. Here we have two devices. The laptop (which is the source device) and the google.com server (which is the target device).

The source device creates the data packets which it then sends out to the target device. These data packet's headers are often manipulated by routers while in transit, e.g. when <a href="http://codingbee.net/tutorials/linux/networking/network-address-translation-nat" rel="nofollow">NATs</a> play their port. Not only that, but additional headers are attached (or removed while the data packet is in transit).

<strong>The OSI Model</strong>
Which headers are attached, when they are attached, and the content of the headers, are all defined in the OSI model.
<pre>  OSI LAYERS                              LAPTOP                             GOOGLE                                 OSI LAYERS

+--------------+                        +--------------+                   +--------------+                       +--------------+
|Application   |                        |Firefox       |                   |Google Server |                       |Application   |
+--------------+                        +--------------+                   +--------------+                       +--------------+

+--------------+                        +--------------+                   +--------------+                       +--------------+
|Presentation  |                        |Web Page      |                   |Web Page      |                       |Presentation  |
+--------------+                        +--------------+                   +--------------+                       +--------------+

+--------------+                        +--------------+                   +--------------+                       +--------------+
|Session       |                        |HTTP GET      |                   |HTTP GET      |                       |Session       |
+--------------+                        +--------------+                   +--------------+                       +--------------+

+--------------+                   +-------------------+                   +-------------------+                  +--------------+
|Transport     |                   |TCP |Payload       |                   |Payload       |TCP |                  |Transport     |
+--------------+                   +-------------------+                   +-------------------+                  +--------------+

+--------------+               +-----------------------+                   +-----------------------+              +--------------+
|Network       |               |IP |TCP |Payload       |                   |Payload       |TCP |IP |              |Network       |
+--------------+               +-----------------------+                   +-----------------------+              +--------------+

+--------------+        +------------------------------+                   +-----------------------------+        +--------------+
|Data          |        |Frame |IP |TCP |Payload       |                   |Payload       |TCP |IP |Frame|        |Data          |
+--------------+        +------------------------------+                   +-----------------------------+        +--------------+

+--------------+                        +--------------+                   +--------------+                       +--------------+
|Physical      |                        |Binary        |                   |Binary        |                       |Physical      |
+--------------+                        +----+---+-----+                   +----+----+----+                       +--------------+
                                             ^   |                              ^    |
                                             |   |           Request            |    |
                                             |   +------------------------------+    |
                                             |                                       |
                                             |               Response                |
                                             +---------------------------------------+


</pre>