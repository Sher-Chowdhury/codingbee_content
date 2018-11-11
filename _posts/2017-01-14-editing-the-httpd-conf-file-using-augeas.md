---
ID: 595
post_title: Editing the httpd.conf file using Augeas
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/editing-the-httpd-conf-file-using-augeas
published: true
post_date: 2017-01-14 00:00:00
---
Sometimes you might want to edit the httpd.conf file using a shell script. For example let's say we have the following file on our CentOS 7 machine:

<pre>
$ cat /etc/httpd/conf/httpd.conf
ServerRoot "/etc/httpd"
Listen 80
Include conf.modules.d/*.conf
User apache
Group apache
ServerAdmin root@localhost
<Directory />
    AllowOverride none
    Require all denied
</Directory>
DocumentRoot "/var/www/html"
<Directory "/var/www">
    AllowOverride None
    Require all granted
</Directory>
<Directory "/var/www/html">
    Options Indexes FollowSymLinks
    AllowOverride None                    # I WANT TO EDIT THIS LINE ONLY
    Require all granted
</Directory>
<IfModule dir_module>
    DirectoryIndex index.html
</IfModule>
<Files ".ht*">
    Require all denied
</Files>
ErrorLog "logs/error_log"
LogLevel warn
<IfModule log_config_module>
    LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined
    LogFormat "%h %l %u %t \"%r\" %>s %b" common
    <IfModule logio_module>
      LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\" %I %O" combinedio
    </IfModule>
    CustomLog "logs/access_log" combined
</IfModule>
<IfModule alias_module>
    ScriptAlias /cgi-bin/ "/var/www/cgi-bin/"
</IfModule>
<Directory "/var/www/cgi-bin">
    AllowOverride None
    Options None
    Require all granted
</Directory>
<IfModule mime_module>
    TypesConfig /etc/mime.types
    AddType application/x-compress .Z
    AddType application/x-gzip .gz .tgz
    AddType text/html .shtml
    AddOutputFilter INCLUDES .shtml
</IfModule>
AddDefaultCharset UTF-8
<IfModule mime_magic_module>
    MIMEMagicFile conf/magic
</IfModule>
EnableSendfile on
IncludeOptional conf.d/*.conf
LimitRequestLine 81900

</pre>

The ls/print/get commands are not important, they are just there to help you see what's happening, and are handy for troubleshooting. The actual command that makes the change is the set command. the 'save' command then applies the change to the httpd.conf file.

The above can now be run automatically inside a bash shell script like this:

<pre>
#!/bin/bash
yum install -y augeas
yum install -y httpd
systemctl start httpd

augtool <<-EOF
set /files/etc/httpd/conf/httpd.conf/Directory[arg='\"/var/www/html\"']/*[self::directive='AllowOverride']/arg ALL
save
quit
EOF

systemctl restart httpd
</pre>

Side note: editing the httpd.conf file isn't necessary, since you can override them by dropping your custom configurations in the conf.d folder instead, thanks to the IncludeOptional apache setting, shown in the above sample file.