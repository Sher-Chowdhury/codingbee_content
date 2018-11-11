---
ID: 2951
post_title: 'Apache &#8211; How does setting up SSL/TLS and HTTPS improve security?'
author: sher
post_excerpt: "Here's a simple example of how to use openssl to create a public-private key-pair, then use that key-pair to encrypt and then decrypt some sample data. "
layout: post
permalink: >
  https://codingbee.net/cookbooks/centos/apache-how-does-setting-up-ssl-tls-and-https-improve-security
published: true
post_date: 2018-03-09 20:05:34
---
Nowadays it's common practice to setup web servers to communicate with web browsers using https? But how does https provide better security than plain old http?

we'll use the <a href="https://en.wikipedia.org/wiki/Alice_and_Bob">Bob and Alice</a> analogy to help explain this. Let's say Alice wants to buy a book from a website called 'www.bobsbooks.com/'. Let's also say that this website is hosted on a webserver that managed by Bob, who hasn't got round to setting up https. So if Alice attempts to make the purchase, then we would have this scenario:
<pre>      Alice's web browser                                     Bob's web server
+------------------------------+                        +-----------------------------+
|                              |                        |                             |
|          Firefox             |                        |      www.bobsbooks.com      |
|                              |                        |                             |
|                              |                        |                             |
|                              |         http           |                             |
|                              <------------------------>                             |
|                              |                        |                             |
|                              |                        |                             |
|                              |                        |                             |
|                              |                        |                             |
|                              |                        |                             |
|                              |                        |                             |
|                              |                        |                             |
+------------------------------+                        +-----------------------------+

</pre>
This situation has 2 problems:
<ol>
 	<li>Eavesdropping - All data travelling between Alice and Bob is in plain text, this means that criminal could potentially get hold of Alice's credit card details</li>
 	<li>Man in the middle attacks - Criminal could build a clone of Bob's website and hijacks Alice's web browser's connection. In which case Alice might end up communicating with the cloned fake website rather than the genuine website. Again her credit card details could end up in the criminal's hands</li>
</ol>

<h3>Solution A - How it doesn't work</h3>
So what Bob could do is utilise Asymmetric Encryption by creating a public-priviate key pair on the web server. First he creates the Private:
<pre>$ openssl genpkey -algorithm RSA -out private_key.pem -pkeyopt rsa_keygen_bits:2048
</pre>
Then from the private key he creates the public key:
<pre>$ openssl rsa -pubout -in private_key.pem -out public_key.pem
</pre>
Now let's imagine Bob makes the public key available to download from his website, and Alice downloads the public key and somehow manages to setup firefox to use the public key to encrypt all data travelling between her web browser and web server. That would fix the eavesdropping problem, but not the man-in-the-middle problem. That's because Alice might end up downloading a public key from the clone fake-website in the first place. That would mean the criminal who owns the fake website would be able to decrypt all the encrypted data Alice sends to them, as they would have the key-pair's private key.


<h3>Solution B - How it kind of works</h3>
Solution A would have worked if there was a way to guarantee that Alice's web browser can tell the difference between a criminal's public key, and a genuine public key. Luckily this is possible with Certificate Authorities (aka CA). Certificate Authorities are companies that are already fully trusted by all the major web browsers (chrome, firefox, Edge,...etc). Here are some <a href="http://pluralsight.pxf.io/c/1198699/424552/7490?u=https%3A%2F%2Fwww.pluralsight.com%2Fblog%2Fsoftware-development%2Ftop-reliable-ssl-certificates">examples of CAs</a>. At it's heart a CA owns a public-private key-pair. However these CA key-pairs are used differently. The private key is used for encrypting data, and the public key decrypts them.




<pre>
      Alice's web browser                                     Bob's web ser^er
+------------------------------+                        +--------------------------------+
|                              |                        |                                |
|          Firefox             |                        |      www.bobsbooks.com         |
|                              |                        |                                |
|                              |                        |                                |
|                              |                        |   public+pri^ate key+pair      |
|                              |                        |  +------------------------+    |
|                              |                        |  |                        |    |
|                              |                        |  |  +------------------+  |    |
|                              |                        |  |  | Bob's public.key |  |    |
|                              |                        |  |  +------------------+  |    |
|                              |                        |  |                        |    |
|                              |                        |  |  +------------------+  |    |
|                              |                        |  |  | Bob's pri^ate.key|  |    |
|                              |                        |  |  +------------------+  |    |
|                              |                        |  |                        |    |
|                              |                        |  +------------------------+    |
+------------------------------+                        +--------------------------------+



              +------------------------------------------------------+
              |                                                      |
              |         Certificate Authority (CA)                   |
              |                                                      |
              |                                                      |
              |        Public+pri^ate key+pair                       |
              |  +-----------------------------------------------+   |
              |  |                                               |   |
              |  |   +---------------------------------------+   |   |
              |  |   | CA's private key (used for encrypting)|   |   |
              |  |   +---------------------------------------+   |   |
              |  |                                               |   |
              |  |   +--------------------------------------+    |   |
              |  |   | CA's Public Key (used for decrypting)|    |   |
              |  |   +--------------------------------------+    |   |
              |  |                                               |   |
              |  +-----------------------------------------------+   |
              |                                                      |
              |                                                      |
              +------------------------------------------------------+

</pre>






Since web browsers already fully trusts the CAs, the  CA's public keys are packaged into special certificates called <a href="https://en.wikipedia.org/wiki/Root_certificate">root certificates</a> and then these root certificates are preinstalled into the web browser software. 

<pre>
      Alice's web browser                                     Bob's web ser^er
+------------------------------+                        +--------------------------------+
|                              |                        |                                |
|          Firefox             |                        |      www.bobsbooks.com         |
|                              |                        |                                |
|                              |                        |                                |
|                              |                        |   public+pri^ate key+pair      |
|                              |                        |  +------------------------+    |
|                              |                        |  |                        |    |
|                              |                        |  |  +------------------+  |    |
|                              |                        |  |  | Bob's public.key |  |    |
|     +------------------+     |                        |  |  +------------------+  |    |
|     | Root Certificate |     |                        |  |                        |    |
|     +--^---------------+     |                        |  |  +------------------+  |    |
|        |                     |                        |  |  | Bob's pri^ate.key|  |    |
|        |                     |                        |  |  +------------------+  |    |
|        |                     |                        |  |                        |    |
|        |                     |                        |  +------------------------+    |
+------------------------------+                        +--------------------------------+
         |
         |
         |
         |    +------------------------------------------------------+
         |    |                                                      |
         |    |         Certificate Authority (CA)                   |
         |    |                                                      |
         |    |                                                      |
         |    |        Public+pri^ate key+pair                       |
         |    |  +-----------------------------------------------+   |
         |    |  |                                               |   |
         |    |  |   +---------------------------------------+   |   |
         |    |  |   | CA's private key (used for encrypting)|   |   |
         |    |  |   +---------------------------------------+   |   |
         |    |  |                                               |   |
         |    |  |   +--------------------------------------+    |   |
         +-----------+ CA's Public Key (used for decrypting)|    |   |
              |  |   +--------------------------------------+    |   |
              |  |                                               |   |
              |  +-----------------------------------------------+   |
              |                                                      |
              |                                                      |
              +------------------------------------------------------+


</pre>








You can view a list of these preinstalled certificates by navigating to your browser's setting->security section. You should find at least a hundred or so root certificates preinstalled.

If a web browser receives encrypted data and is able to decrypt it using one of the preinstalled root certificates, then that proves to the web browser that the encrypted data was from a CA, and therefore the web browser will fully and completely trusts the decrypted data.

So going back to our example of Bob and Alice, what Bob can do is send his public key to a CA so they can encrypt it and the return the encrypted public key back to Bob. Bob can then make this encrypted public key available for download from his web server. Alice's web browser will then download the key from Bob's web server. The web server will then try to decrypt it using one of the preinstalled root certificate. If successfull, then the web browser fully trusts the contents of the encrypted file, which in this case it Bob's public key.  The web browser then randomly generates a long number which is called a session id (aka session key) - which will act as a key for Symmetric encryption. The web browser then encrypts the session id with the public key and sends it back to Bob's web server:

<ul>
	<li>If this encrypted session id, ends up getting sent to the criminal's clone web server, then the criminal won't be able to decrypt the encrypted session-id since the criminal doesn't have a copy of Bob's private key, and consequently the web browser will cease cease communicating with the criminals web server.</li>
	<li>If the encrypted session id ends up getting sent to the Bob's server, then Bob's server will decrypt the encrypted message using it's private key and extract the session id value. It will then symmetrically encrypt messages with the session id and send it back to the web browser. </li>
</ul>



Once Alice's web server receives session id symmetrically encrypted messages from Bob's server, it knows that it is communicating with the real bob's web server and will continue to communicate with it by sending/receiving session-id encrypted data. 


This fixes the 2 problems:

<ul>
	<li>Eavesdropping - Now no longer possible because all communication is now encrypted, Initially using Asymmetric encryption as part of the initial handshake, then by symmetric encription using the session id</li>
	<li>man in the middle attack - Now no longer impossible because the criminal don't possess a copy of Bob's private key which is needed during the initial handshake</li>
</ul>

    
This, in principle is what happens in the real world. A few things to bear in mind:

<ul>
	<li>The session id will expire in various circumstance, e.g. if the web browser is restarted. When the session id expires, then the hand-shake is done again and brand new session key is generated.</li>
	<li>Bob must keep his private key stored securely so that it doesn't fall into the wrong hands. If the criminals do get hold of Bob's private key then they can start doing man in the middle attacks.</li>
</ul>





<h3>Solution C - How it really works</h3>

As in solution B, Bob creates a private and public key. But this time we don't create a standalone public key, instead we create something called a CSR (Certificate Signing Request) file which has the public key embedded inside it.

There are a few ways to create the private key and CSR file. For example you can run:

<h4>Option A</h4>
First create the private key

<pre>
$ openssl genpkey -algorithm rsa -pkeyopt rsa_keygen_bits:2048 -out bobsbooks.com.private.key
...+++
..................+++

$ ll
total 4
-rw-r--r--. 1 root root 1704 Mar 10 14:51 bobsbooks.com.private.key
$ cat bobsbooks.com.private.key
-----BEGIN PRIVATE KEY-----
MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQCU5sIz7uBnyaro
/4IHQA6Ryhnqspgqh7cazsfTO+OpOZ2Nf+YmhHBKqEmX6TdyB5hYd5gDp/0Png1/
9teErEB5K71iGNp18+heM0iC5MKRC4F0Ec3UPPJg9t6iMpf9jHNTbv4zbCgr0Omf
r9yzxjAEu+DlQdhLeLkLU7IyxDNi4BHMt1kmlpWCyg4iN3DKBp8dJ0FttGTGGwmO
hXLgclQe6+7XVNa5mJlhRhsc2i01igpZv/7pvZJbUnREqx2gbC1tomvRzu3KzqYK
1EoUIky7+Rvoj3Qy8hJLbFTkNbC/4TuD8FfaVb5eOAPCLDaMGeDlr5FnOE16EAQ9
4nLJPuvtAgMBAAECggEAZ2LR9L0nkuizgpPl9KLFevSYihz3CpCw0ixBEqnCqP0Y
J+dmJdhPH4c5kgCcPtCHNuXS5rb9Ppq/RNpfSf9PxNJZ7ILVLJDuBnoVHs03Sv5y
I+U4xOSMZkcCzhvS7HlYmdW3Kn0uZz3m+LlixHAXZzIMNSJCzCRGV1cFo7oW888R
bBllnYEZg0wcVnVW+d5MgZZYQEUS5HdVwy9n6/aVL6EmTLh1qgDFDZc6J7sigemX
znDKGwoFLLkq2Y2FwrAT7tykiciddDFTDzzgRhBf1f7oESyLFe2mnMLg98CgTHS9
oCCQeOsRMAWvlEc8nadk8q3+g7mInPKir6BwNFHpAQKBgQDEL/LYocCojfF/Zwl3
WzgnMvc/0d1G5whfTY+D09Ta79Axo/SAID0bEhPmnrwPzMRLGllLWtdEpsfhrQUL
vMRzZ5tGZ//ebYaBSSv28c498kSph3QZ5sEHFHeyRzLL9aKGakobd1Lf7vizYacx
zMlLqb0PAMU47CjUtkutRwy1lwKBgQDCTDvw6Fp6EuEFWvHzC9n0UJuEisCvdeEC
f5TWDSnji7J6sTkUJ6Io5S4zaxhL34XfVmV7rtTW8FHpQrflgttyju+iIvvYp17J
TIyxc8mVO6t0zysJRWQw9kWpH+H+R8lFubRdXlsGm8P/N+KJwrZ2176+nCSEw/Aw
asRR0FEDGwKBgFjnrbJUIt1/bZghll2bXegNpZQrQ4509gtSN54y6bvJf1xvg0Zo
1agl2hThjDHQxoeOSjbm1LTMq7nuUWIPSitOIUSXrpEaqyB9i0neLGtQFaOJuahf
NMxDjzagIzngd6VYF3s46LqILfYEY+MifXonUoCxBa8U/i/e6I5+qxjDAoGALfe9
QqymIzVuOUcf8OF/6jZb5Mqy9ZAM8PtgKhgpLeVyD2b4WxVZXxWBjbaro9+5G3Kk
RdPCFSv8JRScz1bbNkos1Y4zSTmnyjYrHxyGt6DLWrJcuy/ImXDaYD3jyLYRH0bl
LiXRoUme4DXHidIgOxOl1BC67xIwNDQ+SCDh2bsCgYEAn5FmwxpW8WoVi7LTbR3o
i/pLevU/TvqrRlHVDisuQBpbe4T/JGtcZK3Wa9PFt8N3AYVCI1yNnnMFKnfoMddt
E9y9C39U1M4dIYFFWfcYbRUVczinnDJ6ZUobaWStH/IFVW0u64PKJk9CR8S/w9ef
akoQTJMpvcgEdpTKoeyh9vI=
-----END PRIVATE KEY-----
</pre>

Then we create the CSR. The CSR is basically an application form which has several fields that needs to be filled in. As a result we get prompted to provide additional information during the CSR creation:

<pre>
$ openssl req -new -key bobsbooks.com.private.key -out bobsbooks.com.csr
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:UK
State or Province Name (full name) []:Hampshire
Locality Name (eg, city) [Default City]:Southampton
Organization Name (eg, company) [Default Company Ltd]:Bob's books Ltd
Organizational Unit Name (eg, section) []:IT Department
Common Name (eg, your name or your server's hostname) []:bobsbooks.com
Email Address []:admin@bobsbooks.com

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
</pre>


The information provided in these fields is important. They will used by the CA so that can do some vetting and background checks. In particular the Common Name needs to match the web server's url. The last 2 fields are optional so we left them blank. This ends up creating:

<pre>
$ ll
total 8
-rw-r--r--. 1 root root 1090 Mar 10 14:57 bobsbooks.com.csr
-rw-r--r--. 1 root root 1704 Mar 10 14:51 bobsbooks.com.private.key

$ cat bobsbooks.com.csr
-----BEGIN CERTIFICATE REQUEST-----
MIIC6zCCAdMCAQAwgaUxCzAJBgNVBAYTAlVLMRIwEAYDVQQIDAlIYW1wc2hpcmUx
FDASBgNVBAcMC1NvdXRoYW1wdG9uMRgwFgYDVQQKDA9Cb2IncyBib29rcyBMdGQx
FjAUBgNVBAsMDUlUIERlcGFydG1lbnQxFjAUBgNVBAMMDWJvYnNib29rcy5jb20x
IjAgBgkqhkiG9w0BCQEWE2FkbWluQGJvYnNib29rcy5jb20wggEiMA0GCSqGSIb3
DQEBAQUAA4IBDwAwggEKAoIBAQCU5sIz7uBnyaro/4IHQA6Ryhnqspgqh7cazsfT
O+OpOZ2Nf+YmhHBKqEmX6TdyB5hYd5gDp/0Png1/9teErEB5K71iGNp18+heM0iC
5MKRC4F0Ec3UPPJg9t6iMpf9jHNTbv4zbCgr0Omfr9yzxjAEu+DlQdhLeLkLU7Iy
xDNi4BHMt1kmlpWCyg4iN3DKBp8dJ0FttGTGGwmOhXLgclQe6+7XVNa5mJlhRhsc
2i01igpZv/7pvZJbUnREqx2gbC1tomvRzu3KzqYK1EoUIky7+Rvoj3Qy8hJLbFTk
NbC/4TuD8FfaVb5eOAPCLDaMGeDlr5FnOE16EAQ94nLJPuvtAgMBAAGgADANBgkq
hkiG9w0BAQsFAAOCAQEAZwoAs2I2E+bG7wQQW+wfVP5VwVAwvMquxmGkRJ2Y1ZuE
oJNga4MCYqNzlhBV85CehQAieAofRRzQ5gOKNXLORGYIGWVE1xJdAAq52zsfp6b6
qYT1OmFfFEiJNze0sB9RSC0Ca/T/a03TVsUuQ0Znasy+B4azghJMBmczNV5jsHYz
Ex+FcNm2HrN27vNUQAmoYLw1Ic0evLtJyBCuEQPJ0vp8QJpTCNZ+CJu+Q5pgeSUU
f15tgUUvOYmRPYuOO4ScUORAiOGCy10/rxMs0DQ9w+c1EfOscB21Lq7WNVxFdVy1
gYKlxHNOigkfXES3c0LXaKDiMJFNKQQy4h68Ejd0AA==
-----END CERTIFICATE REQUEST-----
</pre> 

This isn't encrypted. You can query the contents of a CSR using openssl:

<pre>
$ openssl req -text -noout -verify -in  bobsbooks.com.csr
verify OK
Certificate Request:
    Data:
        Version: 0 (0x0)
        Subject: C=UK, ST=Hampshire, L=Southampton, O=Bob's books Ltd, OU=IT Department, CN=bobsbooks.com/emailAddress=admin@bobsbooks.com
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:94:e6:c2:33:ee:e0:67:c9:aa:e8:ff:82:07:40:
                    0e:91:ca:19:ea:b2:98:2a:87:b7:1a:ce:c7:d3:3b:
                    e3:a9:39:9d:8d:7f:e6:26:84:70:4a:a8:49:97:e9:
                    37:72:07:98:58:77:98:03:a7:fd:0f:9e:0d:7f:f6:
                    d7:84:ac:40:79:2b:bd:62:18:da:75:f3:e8:5e:33:
                    48:82:e4:c2:91:0b:81:74:11:cd:d4:3c:f2:60:f6:
                    de:a2:32:97:fd:8c:73:53:6e:fe:33:6c:28:2b:d0:
                    e9:9f:af:dc:b3:c6:30:04:bb:e0:e5:41:d8:4b:78:
                    b9:0b:53:b2:32:c4:33:62:e0:11:cc:b7:59:26:96:
                    95:82:ca:0e:22:37:70:ca:06:9f:1d:27:41:6d:b4:
                    64:c6:1b:09:8e:85:72:e0:72:54:1e:eb:ee:d7:54:
                    d6:b9:98:99:61:46:1b:1c:da:2d:35:8a:0a:59:bf:
                    fe:e9:bd:92:5b:52:74:44:ab:1d:a0:6c:2d:6d:a2:
                    6b:d1:ce:ed:ca:ce:a6:0a:d4:4a:14:22:4c:bb:f9:
                    1b:e8:8f:74:32:f2:12:4b:6c:54:e4:35:b0:bf:e1:
                    3b:83:f0:57:da:55:be:5e:38:03:c2:2c:36:8c:19:
                    e0:e5:af:91:67:38:4d:7a:10:04:3d:e2:72:c9:3e:
                    eb:ed
                Exponent: 65537 (0x10001)
        Attributes:
            a0:00
    Signature Algorithm: sha256WithRSAEncryption
         67:0a:00:b3:62:36:13:e6:c6:ef:04:10:5b:ec:1f:54:fe:55:
         c1:50:30:bc:ca:ae:c6:61:a4:44:9d:98:d5:9b:84:a0:93:60:
         6b:83:02:62:a3:73:96:10:55:f3:90:9e:85:00:22:78:0a:1f:
         45:1c:d0:e6:03:8a:35:72:ce:44:66:08:19:65:44:d7:12:5d:
         00:0a:b9:db:3b:1f:a7:a6:fa:a9:84:f5:3a:61:5f:14:48:89:
         37:37:b4:b0:1f:51:48:2d:02:6b:f4:ff:6b:4d:d3:56:c5:2e:
         43:46:67:6a:cc:be:07:86:b3:82:12:4c:06:67:33:35:5e:63:
         b0:76:33:13:1f:85:70:d9:b6:1e:b3:76:ee:f3:54:40:09:a8:
         60:bc:35:21:cd:1e:bc:bb:49:c8:10:ae:11:03:c9:d2:fa:7c:
         40:9a:53:08:d6:7e:08:9b:be:43:9a:60:79:25:14:7f:5e:6d:
         81:45:2f:39:89:91:3d:8b:8e:3b:84:9c:50:e4:40:88:e1:82:
         cb:5d:3f:af:13:2c:d0:34:3d:c3:e7:35:11:f3:ac:70:1d:b5:
         2e:ae:d6:35:5c:45:75:5c:b5:81:82:a5:c4:73:4e:8a:09:1f:
         5c:44:b7:73:42:d7:68:a0:e2:30:91:4d:29:04:32:e2:1e:bc:
         12:37:74:00

</pre>



Bob then forward
 



&nbsp;

&nbsp;

&nbsp;
Asymetric encryption is used as part of the initial handshake between the web browser (e.g. firefox) and the web server. This initial handshake lasts just long enough for the web browser to send a Symmetric Encryption Key (aka session id), and after that all subsequent data that travels between the web server and firefox is encrypted using the session id, during the lifetime of the web session. Here's a summary of the steps: 


<a href="https://en.wikipedia.org/wiki/Public_key_infrastructure">PKI (Public Key Infrastructure)</a> - This is the name of the whole setup where a CA is used to establish a <a href="https://en.wikipedia.org/wiki/Chain_of_trust">chain of trust</a>.



<ol>
	<li>The web server shares out the public key to web browsers - which is done using CSR files and Certificate Authorities.</li>
<li>the web browser then randomly creates a very long number which is to be used as the <strong>Session Key</strong> or <strong>Session ID</strong>.</li>
	<li>Firefox then encrypts this session id with the the public-key that it received from the web server.</li>
	<li>Then it sends the encrypted session id to the web server.</li>
	<li>The web server can decrypt this encrypted session-id using it's private key. Note, the private key must be kept safe and must never end up in the wrong hands.</li>
	<li>Once that's done, the web server starts using the session id to encrypt any further data that it sends to firefox.</li>
 	<li>As soon as firefox receives the session-id encrypted data, it then uses the session id to decrypt the data</li>
	<li>all further communication between firefox and the web server is encrypted/decrypted using this session id.</li>
</ol>
&nbsp;

&nbsp;

&nbsp;

https://en.wikipedia.org/wiki/Chain_of_trust