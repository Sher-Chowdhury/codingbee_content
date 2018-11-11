---
ID: 2976
post_title: >
  RHCE – Make websites more secure by
  setting up HTTPS and SSL/TLS for CentOS
  7
author: sher
post_excerpt: >
  This is walktrhough how to generate ssl
  certificates and then configure https on
  a CentOS 7 Apache server to use the ssl
  certificate.
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/rhce-make-websites-more-secure-by-setting-up-https-and-ssl-tls-for-centos-7
published: true
post_date: 2018-03-11 13:08:44
---
So far we have configured web servers to allow connections via the http protocol. However http is not secure which is why <a href="http://codingbee.net/cookbooks/centos/apache-how-does-setting-up-ssl-tls-and-https-improve-security">it's better to use https</a>.

When using https, we actually encrypt all data traffic using <a href="http://codingbee.net/cookbooks/centos/openssl-demo-encrypting-decrypting-files-using-both-symmetric-and-asymmetric-encryption">Symmetric and Asymmetric Encryption</a>.

To setup the above encryption system on our Apache server, we need to first to install the ssl addon module that will allow Apache to be able to communicate on the SSL/TLS layer:

<pre>
$ yum install mod_ssl
</pre>

Next we need to install the software that is used for generating public-private key-pairs, so that we can create a key-pair for our web server.

<pre>
$ yum install openssl
</pre>



Next we create the private key and it's csr file. We generate the private key by running the following:

<pre>
$ openssl genpkey -algorithm RSA -out cb.com.private.key -pkeyopt rsa_keygen_bits:2048
...+++
.......................................+++
</pre>
Here, we specified private key options (pkeyopt) so that we can specify complexity of rsa_keygen_bits:2048. For more info, see:

<pre>
$ man genpkey
</pre>

This ends up creating the private key, which looks something like this:


<pre>
$ ll
total 4
-rw-r--r--. 1 root root 1704 Mar 11 10:37 cb.com.private.key

$ cat cb.com.private.key
-----BEGIN PRIVATE KEY-----
MIIEvgIBADANBgkqhkiG9w0BAQEFAASCBKgwggSkAgEAAoIBAQCvcVQdXcbGA3Dr
E9jpxaLsF8b9j9feiQRdpR9gSOM4CE0bPHkdnVSpXdy+6ARTsDXCgT+OLD2mdK1G
wjvN4cYnUJkZGkIRcnarlf9Rx0YqFmF3VfyRk+rd7RZ9ifmJMXSZYPyc4WTyMwuD
LlPqrZPM/wVeyeAQeJKO7YbzBdG/Zs6+smRPq2tmrfJQn3F9/FL+mpp5bGdoIYXK
dkJRxmfyr/hCGxx9MiOtU80rE1XQOLh5FgIg8baNXQTHNYV19aRI8suUfBtmdosX
LVI5vFkVLx/no5SXv09vCqVVXFepjC1lPzkFYU8fqxFanBVbs0e3oJgTqEnw63Sn
PLxkI5lHAgMBAAECggEBAJpab7HCfo5aGq+H77CE5DkpFjahpj+wdrnY+8jfd6El
lIkg5EIM2J6F6R/Ay4XBoxq0UfFItRjM0GJPowQY3hhazceLFZN6DDQcLNyLIQ0I
UcoHScXeyKVl4QKkMHnmm/oRnU588y4vgLJyQPxYqI+T0uq5W7vCCNdc3PzVItts
KA59NVKV5VYZ8VvMNxhfdsBW5Fo/8eChSeHU1CcnkVIew2JJMXWQXCCndox0sOdv
rphNSYrM8x6gKHlOBblaeHfu4m+gSZbmEJm3TfWRSXpzqSnY8HQiN1MtMy6lLou2
BXqiK54HOtOgnCopOJDL8NDujzJIn+u/HfqWhSPQI4ECgYEA1b6vGV/pt3zFrYPJ
zXVlYUllBbztecbvCxVI4dGHxqrGLGUjuXsaBpn8+3qJ8MO7YgpNO7TWQ2f7mZL4
SfOC+o/LlOJZ7O5EvD6jXituoez7mqQKIeorraFI26a/6u+RI9xCQ8e/U4eflByj
OpK21yHT6NLtTcs/xGW75Y68KQcCgYEA0iA6Qm0aU/ICRWxBXvI26cCfFUwdw9WZ
vXu1YUPC3IBrYaEoi+II1TPSv8C1zi9af2QP/L55WFb9n22yuItUzjMccx///2/V
9Sd9UsJqb9ufDVg6CaXDkUyEpPghMcz3sE/+pgFm23nx48PGidrGRlcRC4uxxXEI
hgU74ULuPcECgYAyylXnTLBHxqFeguNwLAZEIOGdhHBVsJza4FrqNhTyc2AV6o6b
o5HiobLIuyyOBqS/Pj1uA6GqcMoLiy3uvKuSCJtSrJq7vz6cELJXTTDDz4FWFMbH
mlJ4tsuOTkhqdjTh9ZPVcWCYY7DazBdJctV5t3MYbw6unq5vZ9FHUiyJ/wKBgQCm
IICvH98e20lcZbhRPqKXsIiVp/YobGn+Wx4N5irbdlRvfaC252AgX7aRj+StPNcJ
vKy/IECDzVL7hxkzukS0OciWZIhbBapdBnmYwsiv4y0eudA47GtwJtUAMITeALUb
GkO0L4uIniLV9mSzfNohpph9TlagSTt56g1rjSUagQKBgGYp6Nu17IbIKoPyoBaD
ZuMf7CLIHUnlV1bMmeAA3W74yPsaljq7UP9jE28RmvxANgRyI1iM5uDb1nzl9sXP
PM2rbG0ISap3q1Yxps59GPhJ5LMTLo7idBloptuZiNkxFU8fVBD90LBHel7XIXlk
X/JOfQPQimuvp7/xu3m84rPb
-----END PRIVATE KEY-----
</pre>

This file should be kept safe and secure and not shared with anyone. Next to create the csr file, we run:


<pre>
[root@webserver ~]# openssl req -new -key example.com.private.key -out example.com.csr
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
Organization Name (eg, company) [Default Company Ltd]:Example Ltd
Organizational Unit Name (eg, section) []:IT Department
Common Name (eg, your name or your server's hostname) []:example.com
Email Address []:admin@example.com

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
</pre>

You will be prompted to provide some information. The key one is that you ensure you enter the correct hostname (web address). This creates the file:

<pre>
$ ll example.com.csr
-rw-r--r--. 1 root root 1078 Mar 11 10:42 example.com.csr

$ cat example.com.private.csr
-----BEGIN CERTIFICATE REQUEST-----
MIIC4zCCAcsCAQAwgZ0xCzAJBgNVBAYTAlVLMRIwEAYDVQQIDAlIYW1wc2hpcmUx
FDASBgNVBAcMC1NvdXRoYW1wdG9uMRQwEgYDVQQKDAtFeGFtcGxlIEx0ZDEWMBQG
A1UECwwNSVQgRGVwYXJ0bWVudDEUMBIGA1UEAwwLZXhhbXBsZS5jb20xIDAeBgkq
hkiG9w0BCQEWEWFkbWluQGV4YW1wbGUuY29tMIIBIjANBgkqhkiG9w0BAQEFAAOC
AQ8AMIIBCgKCAQEAr3FUHV3GxgNw6xPY6cWi7BfG/Y/X3okEXaUfYEjjOAhNGzx5
HZ1UqV3cvugEU7A1woE/jiw9pnStRsI7zeHGJ1CZGRpCEXJ2q5X/UcdGKhZhd1X8
kZPq3e0WfYn5iTF0mWD8nOFk8jMLgy5T6q2TzP8FXsngEHiSju2G8wXRv2bOvrJk
T6trZq3yUJ9xffxS/pqaeWxnaCGFynZCUcZn8q/4QhscfTIjrVPNKxNV0Di4eRYC
IPG2jV0ExzWFdfWkSPLLlHwbZnaLFy1SObxZFS8f56OUl79PbwqlVVxXqYwtZT85
BWFPH6sRWpwVW7NHt6CYE6hJ8Ot0pzy8ZCOZRwIDAQABoAAwDQYJKoZIhvcNAQEL
BQADggEBAI8hsSuKvFqgNXlIcTxpBYa+g5ExdI+kpSJoPL3frXkA2W53qKIXvywa
Mz8UWt+EfXzLCYsuudMYAb2xsvZV2gGpadqXBrxRZ8xxUPoEvivvg0qh0xgAjmZp
QmLhgcHl2ON/pUVHryutWB92XAGi5AC2DNktP3Y7uJmcV00g83x8ngcSTHs7OYQz
HfRpvd4N/m3zFYjrQ19oJ8VNkku8lgGzspsXcxnZJSapvf726NcPsy8HGGrNDoLW
VhMypG8mXDe2vSNgphnlNRjCZT7XP0efZVm8MEoLDln162D4Cr+Ne4RhXvrnXjlM
S0b+/wpyGFpibzorFkLoG5OFrwvZ09k=
-----END CERTIFICATE REQUEST-----
</pre>


You can inspect the content of the csr file like this:


<pre>
$ openssl req -text -noout -verify -in example.com.csr
verify OK
Certificate Request:
    Data:
        Version: 0 (0x0)
        Subject: C=UK, ST=Hampshire, L=Southampton, O=Example Ltd, OU=IT Department, CN=example.com/emailAddress=admin@example.com
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:af:71:54:1d:5d:c6:c6:03:70:eb:13:d8:e9:c5:
                    a2:ec:17:c6:fd:8f:d7:de:89:04:5d:a5:1f:60:48:
                    e3:38:08:4d:1b:3c:79:1d:9d:54:a9:5d:dc:be:e8:
                    04:53:b0:35:c2:81:3f:8e:2c:3d:a6:74:ad:46:c2:
                    3b:cd:e1:c6:27:50:99:19:1a:42:11:72:76:ab:95:
                    ff:51:c7:46:2a:16:61:77:55:fc:91:93:ea:dd:ed:
                    16:7d:89:f9:89:31:74:99:60:fc:9c:e1:64:f2:33:
                    0b:83:2e:53:ea:ad:93:cc:ff:05:5e:c9:e0:10:78:
                    92:8e:ed:86:f3:05:d1:bf:66:ce:be:b2:64:4f:ab:
                    6b:66:ad:f2:50:9f:71:7d:fc:52:fe:9a:9a:79:6c:
                    67:68:21:85:ca:76:42:51:c6:67:f2:af:f8:42:1b:
                    1c:7d:32:23:ad:53:cd:2b:13:55:d0:38:b8:79:16:
                    02:20:f1:b6:8d:5d:04:c7:35:85:75:f5:a4:48:f2:
                    cb:94:7c:1b:66:76:8b:17:2d:52:39:bc:59:15:2f:
                    1f:e7:a3:94:97:bf:4f:6f:0a:a5:55:5c:57:a9:8c:
                    2d:65:3f:39:05:61:4f:1f:ab:11:5a:9c:15:5b:b3:
                    47:b7:a0:98:13:a8:49:f0:eb:74:a7:3c:bc:64:23:
                    99:47
                Exponent: 65537 (0x10001)
        Attributes:
            a0:00
    Signature Algorithm: sha256WithRSAEncryption
         8f:21:b1:2b:8a:bc:5a:a0:35:79:48:71:3c:69:05:86:be:83:
         91:31:74:8f:a4:a5:22:68:3c:bd:df:ad:79:00:d9:6e:77:a8:
         a2:17:bf:2c:1a:33:3f:14:5a:df:84:7d:7c:cb:09:8b:2e:b9:
         d3:18:01:bd:b1:b2:f6:55:da:01:a9:69:da:97:06:bc:51:67:
         cc:71:50:fa:04:be:2b:ef:83:4a:a1:d3:18:00:8e:66:69:42:
         62:e1:81:c1:e5:d8:e3:7f:a5:45:47:af:2b:ad:58:1f:76:5c:
         01:a2:e4:00:b6:0c:d9:2d:3f:76:3b:b8:99:9c:57:4d:20:f3:
         7c:7c:9e:07:12:4c:7b:3b:39:84:33:1d:f4:69:bd:de:0d:fe:
         6d:f3:15:88:eb:43:5f:68:27:c5:4d:92:4b:bc:96:01:b3:b2:
         9b:17:73:19:d9:25:26:a9:bd:fe:f6:e8:d7:0f:b3:2f:07:18:
         6a:cd:0e:82:d6:56:13:32:a4:6f:26:5c:37:b6:bd:23:60:a6:
         19:e5:35:18:c2:65:3e:d7:3f:47:9f:65:59:bc:30:4a:0b:0e:
         59:f5:eb:60:f8:0a:bf:8d:7b:84:61:5e:fa:e7:5e:39:4c:4b:
         46:fe:ff:0a:72:18:5a:62:6f:3a:2b:16:42:e8:1b:93:85:af:
         0b:d9:d3:d9
</pre>

Now we need send the csr file to a CA to get it signed. However for testing purposes we will self-sign this csr.

<pre>
[root@webserver ~]# openssl x509 -req -days 365 -signkey example.com.private.key -in example.com.csr -out example.com.crt
Signature ok
subject=/C=UK/ST=Hampshire/L=Southampton/O=Example Ltd/OU=IT Department/CN=example.com/emailAddress=admin@example.com
Getting Private key
</pre> 

This ends up creating the following:


<pre>
[root@webserver ~]# ll example.com.crt
-rw-r--r--. 1 root root 1350 Mar 11 11:04 example.com.crt
[root@webserver ~]# cat example.com.crt
-----BEGIN CERTIFICATE-----
MIIDuDCCAqACCQCJPsUNdjgPSTANBgkqhkiG9w0BAQsFADCBnTELMAkGA1UEBhMC
VUsxEjAQBgNVBAgMCUhhbXBzaGlyZTEUMBIGA1UEBwwLU291dGhhbXB0b24xFDAS
BgNVBAoMC0V4YW1wbGUgTHRkMRYwFAYDVQQLDA1JVCBEZXBhcnRtZW50MRQwEgYD
VQQDDAtleGFtcGxlLmNvbTEgMB4GCSqGSIb3DQEJARYRYWRtaW5AZXhhbXBsZS5j
b20wHhcNMTgwMzExMTEwNDA0WhcNMTkwMzExMTEwNDA0WjCBnTELMAkGA1UEBhMC
VUsxEjAQBgNVBAgMCUhhbXBzaGlyZTEUMBIGA1UEBwwLU291dGhhbXB0b24xFDAS
BgNVBAoMC0V4YW1wbGUgTHRkMRYwFAYDVQQLDA1JVCBEZXBhcnRtZW50MRQwEgYD
VQQDDAtleGFtcGxlLmNvbTEgMB4GCSqGSIb3DQEJARYRYWRtaW5AZXhhbXBsZS5j
b20wggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCvcVQdXcbGA3DrE9jp
xaLsF8b9j9feiQRdpR9gSOM4CE0bPHkdnVSpXdy+6ARTsDXCgT+OLD2mdK1GwjvN
4cYnUJkZGkIRcnarlf9Rx0YqFmF3VfyRk+rd7RZ9ifmJMXSZYPyc4WTyMwuDLlPq
rZPM/wVeyeAQeJKO7YbzBdG/Zs6+smRPq2tmrfJQn3F9/FL+mpp5bGdoIYXKdkJR
xmfyr/hCGxx9MiOtU80rE1XQOLh5FgIg8baNXQTHNYV19aRI8suUfBtmdosXLVI5
vFkVLx/no5SXv09vCqVVXFepjC1lPzkFYU8fqxFanBVbs0e3oJgTqEnw63SnPLxk
I5lHAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAIO5wY9YKXJjuLu+yPPXbGCUb/Sk
5dGs5EIQrM4KlIJsmgxtHj+IntAOErdhc+sD7OgKrjCJDv60nt7Hf+xUWgJWtZez
mIqBBauqexh6IcK9LWrLMFFpdtD85cCaRcyj7jNEISHcozyLJbnpsoTe2OHNB/sG
hHaLrTg8jqzdrO7P22PW8y9kwx7J4mfyJ/xiiOfMS4dMbgitDhLTk8pDo4fHJy0e
qPmsULicppoBXxNTJ3NUXjol1GizaoAp/LYQ8GQm/cV9kqBGJZZMPdpzXgyOZ0Z9
bfJVAyDHeQUf0eWPYn0psNomclc8G7WGYJHVmR83NTxK3kHuUVFN5xbz3sY=
-----END CERTIFICATE-----
</pre>

This file is referred as a <strong>digital certificate</strong>, or <strong>ssl certificate</strong>, or just <strong>certificate</strong>. The web server needs to forward this certificate to any web browsers as part of the initial https handshake to establish an encrypted session. 

You can inspect this certificate by running:

<pre>
$ openssl x509 -in example.com.crt -text -noout
Certificate:
    Data:
        Version: 1 (0x0)
        Serial Number:
            89:3e:c5:0d:76:38:0f:49
    Signature Algorithm: sha256WithRSAEncryption
        Issuer: C=UK, ST=Hampshire, L=Southampton, O=Example Ltd, OU=IT Department, CN=example.com/emailAddress=admin@example.com
        Validity
            Not Before: Mar 11 11:04:04 2018 GMT
            Not After : Mar 11 11:04:04 2019 GMT
        Subject: C=UK, ST=Hampshire, L=Southampton, O=Example Ltd, OU=IT Department, CN=example.com/emailAddress=admin@example.com
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:af:71:54:1d:5d:c6:c6:03:70:eb:13:d8:e9:c5:
                    a2:ec:17:c6:fd:8f:d7:de:89:04:5d:a5:1f:60:48:
                    e3:38:08:4d:1b:3c:79:1d:9d:54:a9:5d:dc:be:e8:
                    04:53:b0:35:c2:81:3f:8e:2c:3d:a6:74:ad:46:c2:
                    3b:cd:e1:c6:27:50:99:19:1a:42:11:72:76:ab:95:
                    ff:51:c7:46:2a:16:61:77:55:fc:91:93:ea:dd:ed:
                    16:7d:89:f9:89:31:74:99:60:fc:9c:e1:64:f2:33:
                    0b:83:2e:53:ea:ad:93:cc:ff:05:5e:c9:e0:10:78:
                    92:8e:ed:86:f3:05:d1:bf:66:ce:be:b2:64:4f:ab:
                    6b:66:ad:f2:50:9f:71:7d:fc:52:fe:9a:9a:79:6c:
                    67:68:21:85:ca:76:42:51:c6:67:f2:af:f8:42:1b:
                    1c:7d:32:23:ad:53:cd:2b:13:55:d0:38:b8:79:16:
                    02:20:f1:b6:8d:5d:04:c7:35:85:75:f5:a4:48:f2:
                    cb:94:7c:1b:66:76:8b:17:2d:52:39:bc:59:15:2f:
                    1f:e7:a3:94:97:bf:4f:6f:0a:a5:55:5c:57:a9:8c:
                    2d:65:3f:39:05:61:4f:1f:ab:11:5a:9c:15:5b:b3:
                    47:b7:a0:98:13:a8:49:f0:eb:74:a7:3c:bc:64:23:
                    99:47
                Exponent: 65537 (0x10001)
    Signature Algorithm: sha256WithRSAEncryption
         83:b9:c1:8f:58:29:72:63:b8:bb:be:c8:f3:d7:6c:60:94:6f:
         f4:a4:e5:d1:ac:e4:42:10:ac:ce:0a:94:82:6c:9a:0c:6d:1e:
         3f:88:9e:d0:0e:12:b7:61:73:eb:03:ec:e8:0a:ae:30:89:0e:
         fe:b4:9e:de:c7:7f:ec:54:5a:02:56:b5:97:b3:98:8a:81:05:
         ab:aa:7b:18:7a:21:c2:bd:2d:6a:cb:30:51:69:76:d0:fc:e5:
         c0:9a:45:cc:a3:ee:33:44:21:21:dc:a3:3c:8b:25:b9:e9:b2:
         84:de:d8:e1:cd:07:fb:06:84:76:8b:ad:38:3c:8e:ac:dd:ac:
         ee:cf:db:63:d6:f3:2f:64:c3:1e:c9:e2:67:f2:27:fc:62:88:
         e7:cc:4b:87:4c:6e:08:ad:0e:12:d3:93:ca:43:a3:87:c7:27:
         2d:1e:a8:f9:ac:50:b8:9c:a6:9a:01:5f:13:53:27:73:54:5e:
         3a:25:d4:68:b3:6a:80:29:fc:b6:10:f0:64:26:fd:c5:7d:92:
         a0:46:25:96:4c:3d:da:73:5e:0c:8e:67:46:7d:6d:f2:55:03:
         20:c7:79:05:1f:d1:e5:8f:62:7d:29:b0:da:26:72:57:3c:1b:
         b5:86:60:91:d5:99:1f:37:35:3c:4a:de:41:ee:51:51:4d:e7:
         16:f3:de:c6
</pre>


<h2>Configure Apache to make use of our digitial Certificate</h2>

Now we need to setup our test website, www.example.com and configure it to use https. So far we have created the private key and digital certificate in the root's home directory, these needs to be placed in a more sensible location:

<pre>
[root@webserver ~]# cp /root/example.com.crt /etc/pki/tls/certs/
[root@webserver ~]# cp /root/example.com.private.key /etc/pki/tls/private/
</pre> 

Note: pki stands for 'public key infrastructure.'

Now we'll setup the website, by creating a new vhost, which is:


<pre lang='bash'>
[root@webserver conf.d]# cat /etc/httpd/conf.d/example_com_ssl.conf
<VirtualHost *:443>
    ServerName example.com:443
    ServerAlias example.com:443
    SSLCertificateFile /etc/pki/tls/certs/example.com.crt
    SSLCertificateKeyFile /etc/pki/tls/private/example.com.private.key
    DocumentRoot /var/www/html/example_com
    ErrorLog /var/log/httpd/example_com_error.log
    CustomLog /var/log/httpd/example_com_access.log combined
</VirtualHost>
</pre>


Now we do a couple of quick checks:


<pre>
[root@webserver conf.d]# httpd -t
Syntax OK
[root@webserver conf.d]# httpd -D DUMP_VHOSTS
VirtualHost configuration:
*:443                  is a NameVirtualHost
         default server example.com (/etc/httpd/conf.d/example_com_ssl.conf:1)
         port 443 namevhost example.com (/etc/httpd/conf.d/example_com_ssl.conf:1)
                 alias example.com:443
         port 443 namevhost webserver.local (/etc/httpd/conf.d/ssl.conf:56)
</pre>



Now we restart httpd daemon:

<pre>
$ systemctl restart httpd
</pre>


Next let's allow https in firewalld:


<pre>
[root@webserver conf.d]# firewall-cmd --permanent --add-service=https
success
[root@webserver conf.d]# systemctl restart firewalld
[root@webserver conf.d]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: ssh dhcpv6-client https
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
</pre>



Everything should now be setup. We now need to add an entry to hosts file for example.com, then we are ready of testing. We can test this in a few ways. First using curl:


<pre>
[root@webserver conf.d]# curl https://example.com
curl: (60) Peer's certificate issuer has been marked as not trusted by the user.
More details here: http://curl.haxx.se/docs/sslcerts.html

curl performs SSL certificate verification by default, using a "bundle"
 of Certificate Authority (CA) public keys (CA certs). If the default
 bundle file isn't adequate, you can specify an alternate file
 using the --cacert option.
If this HTTPS server uses a certificate signed by a CA represented in
 the bundle, the certificate verification probably failed due to a
 problem with the certificate (it might be expired, or the name might
 not match the domain name in the URL).
If you'd like to turn off curl's verification of the certificate, use
 the -k (or --insecure) option.
</pre>

Since we used a self-signed certificate rather than a CA signed cert, we got this warning message, so let's override this:


<pre>
[root@webserver conf.d]# curl -k https://example.com
hello....this is a secure connection
</pre>

You can't test this using elinks because elinks doesn't support https. An alternative terminal base browser you can use is lynx:


<pre>
$ yum install lynx
</pre>


Then do:

<pre>
$ lynx https://example.com
</pre>

You'll still get the self-signed warning message, but it let's you continue anyway. 


[post-content post_name=rhsca-quiz] 

For this quiz, assume we are setting up the secure url of 'https://cb.com'.
[accordion]
[toggle title="what are the commands to install the rpms required to setup ssl/tls?"]
$ yum install mod_ssl
$ yum install openssl
[/toggle]
[toggle title="What is the command to create a new private key?"]
$ openssl genpkey -algorithm RSA -out cb.com.private.key -pkeyopt rsa_keygen_bits:2048
[/toggle]
[toggle title="Where can you find more info about this command?"]
$ man genpkey
[/toggle]
[toggle title="What is the command to create the corresponding csr?"]
$ openssl req -new -key example.com.private.key -out example.com.csr
# this will prompt for several info, in particular the common name, which needs to be 'example.com' format. 
[/toggle]
[toggle title="Where can you find more info about this command?"]
$ man req
[/toggle]
[toggle title="What is the command to self-sign the csr in order to generate the crt file?"]
$ openssl x509 -req -days 365 -signkey example.com.private.key -in example.com.csr -out example.com.crt
[/toggle]
[toggle title="Where can you find more info about this command?"]
$ man x509
[/toggle]
[toggle title="Where should the private key and crt file be stored in?"]
the crt file:    /etc/pki/tls/certs/
the private key:  /etc/pki/tls/private/
[/toggle]
[toggle title="What vhost file needs to be created?"]
<pre lang='bash'>
$ cat /etc/httpd/conf.d/cb_com.conf
<VirtualHost *:443>
  ServerName cb.com:443
  ServerAlias www.cb.com:443
  DocumentRoot /var/www/html/secure
  SSLCertificateFile /etc/pki/tls/certs/cb.crt
  SSLCertificateKeyFile /etc/pki/tls/private/cb.private.key
</VirtualHost>

# DONT FORGET TO APPEND PORT NUMBERS!!!
</pre>
[/toggle]
[toggle title="What is the command to open up the firewalls?"]
$ firewall-cmd --permanent --add-service=https
$ systemctl restart firewalld
[/toggle]
[toggle title="What syntax checking can you do"]
$ httpd -t
# and:
$ httpd -D DUMP_VHOSTS
[/toggle]
[toggle title="what do you do next?"]
$ systemctl restart httpd
[/toggle]
[toggle title="what is the command to test this?"]
# append entry in /etc/hosts then run:
$ curl https://cb.com
[/toggle]
[/accordion]