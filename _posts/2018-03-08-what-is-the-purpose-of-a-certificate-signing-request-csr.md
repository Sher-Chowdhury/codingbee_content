---
ID: 2930
post_title: >
  What is the purpose of a Certificate
  Signing Request (CSR)
author: sher
post_excerpt: >
  Here we discuss what roles CSR files and
  Certificate Authorities plays in the
  goal of setting up https on an Apache
  web server.
layout: post
permalink: >
  https://codingbee.net/cookbooks/centos/what-is-the-purpose-of-a-certificate-signing-request-csr
published: true
post_date: 2018-03-08 21:59:15
---
In order for a web browser (e.g. firefox) to establish a secure https session with an https enabled Apache web server, it needs a copy the public key part of the web server's public-private key-pair. This is done by requesting a digital certificate from a Certificate Authority (CA). 


A Certificate Authority is a trusted third party that is fully and completely trusted by all the main web browsers (Firefox, Chrome, Microsoft Edge, Safari....etc ). At it's heart, you can think of a CA has a public-private key-pair. But when it comes CA public-private keys, the private key is used for creating encrypted files. These encrypted files are called Digital Certificates (and are often called SSL Certificates). Whereas the CA's public key (aka root certificates) is used for decrypting encrypted files (which in this case are the ssl certificate). The root certificates are pre-installed on all the web main web browsers. You can view a list of them by viewing your web brower's settings. 


So when a web browser receives a digital certificate and the web browser manages to decrypt the data using one of the preinstalled root certificates, then the web browser knows that one of the trusted source (i.e. CAs) created the digital certificate, therefore the web browser will fully and completely trust the the contents of the digitaldigital certificate.   




You can view a csr's content by using an online tool, or by running:


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


here the embedded public key is shown in hexdump form, you can extract in text form like this:


<pre>
$ openssl req -in bobsbooks.com.csr -noout -pubkey
</pre>







https://www.sslshopper.com/article-most-common-openssl-commands.html


http://searchsecurity.techtarget.com/definition/asymmetric-cryptography

https://app.pluralsight.com/player?course=linux-encryption-security-lpic-3-303&author=david-clinton&name=linux-encryption-security-lpic-3-303-m2&clip=0&mode=live


https://support.microsoft.com/en-us/help/246071/description-of-symmetric-and-asymmetric-encryption

https://stackoverflow.com/questions/18257185/how-does-a-public-key-verify-a-signature/18259395#18259395

https://en.wikipedia.org/wiki/Public-key_cryptography

https://www.digitalocean.com/community/tutorials/openssl-essentials-working-with-ssl-certificates-private-keys-and-csrs

https://en.wikipedia.org/wiki/Root_certificate

https://en.wikipedia.org/wiki/Certificate_authority

https://en.wikipedia.org/wiki/Certificate_signing_request

https://www.shellhacks.com/decode-csr/