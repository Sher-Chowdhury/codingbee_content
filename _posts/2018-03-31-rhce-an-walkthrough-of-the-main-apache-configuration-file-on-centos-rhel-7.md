---
ID: 3354
post_title: >
  RHCE – An Walkthrough of the main
  Apache Configuration file on CentOS/RHEL
  7
author: sher
post_excerpt: "This articles goes over the main settngs in Apache's main config file. "
layout: post
permalink: >
  https://codingbee.net/tutorials/rhce/rhce-an-walkthrough-of-the-main-apache-configuration-file-on-centos-rhel-7
published: true
post_date: 2018-03-31 13:03:03
---
Nearly all of Apache's config files are stored under three high-level directories: 


<pre lang="bash">
[root@webserver httpd]# tree /etc/httpd/conf /etc/httpd/conf.d /etc/httpd/conf.modules.d
<strong>/etc/httpd/conf</strong>
├── httpd.conf
└── magic
<strong>/etc/httpd/conf.d</strong>
├── autoindex.conf
├── fcgid.conf
├── manual.conf           # This came from installing the httpd-manual rpm
├── README
├── ssl.conf              # This came from installing the mod_ssl rpm
├── userdir.conf
└── welcome.conf
<strong>/etc/httpd/conf.modules.d</strong>
├── 00-base.conf
├── 00-dav.conf
├── 00-lua.conf
├── 00-mpm.conf
├── 00-proxy.conf
├── 00-ssl.conf
├── 00-systemd.conf
├── 01-cgi.conf
└── 10-fcgid.conf
</pre>


Other rpms can drop files into the *.d directories. 


There are quite a few config files that comes with apache, but the main ones are <code>/etc/httpd/conf/httpd.conf</code> and <code>/etc/httpd/conf.d/ssl.conf</code>. Here's what httpd.conf looks like:


<pre lang="bash">
[root@webserver conf]# cat /etc/httpd/conf/httpd.conf
#
# This is the main Apache HTTP server configuration file.  It contains the
# configuration directives that give the server its instructions.
# See <URL:http://httpd.apache.org/docs/2.4/> for detailed information.
# In particular, see
# <URL:http://httpd.apache.org/docs/2.4/mod/directives.html>
# for a discussion of each configuration directive.
#
# Do NOT simply read the instructions in here without understanding
# what they do.  They're here only as hints or reminders.  If you are unsure
# consult the online docs. You have been warned.
#
# Configuration and logfile names: If the filenames you specify for many
# of the server's control files begin with "/" (or "drive:/" for Win32), the
# server will use that explicit path.  If the filenames do *not* begin
# with "/", the value of ServerRoot is prepended -- so 'log/access_log'
# with ServerRoot set to '/www' will be interpreted by the
# server as '/www/log/access_log', where as '/log/access_log' will be
# interpreted as '/log/access_log'.

#
# ServerRoot: The top of the directory tree under which the server's
# configuration, error, and log files are kept.
#
# Do not add a slash at the end of the directory path.  If you point
# ServerRoot at a non-local disk, be sure to specify a local disk on the
# Mutex directive, if file-based mutexes are used.  If you wish to share the
# same ServerRoot for multiple httpd daemons, you will need to change at
# least PidFile.
#
ServerRoot "/etc/httpd"

#
# Listen: Allows you to bind Apache to specific IP addresses and/or
# ports, instead of the default. See also the <VirtualHost>
# directive.
#
# Change this to Listen on specific IP addresses as shown below to
# prevent Apache from glomming onto all bound IP addresses.
#
#Listen 12.34.56.78:80
Listen 80

#
# Dynamic Shared Object (DSO) Support
#
# To be able to use the functionality of a module which was built as a DSO you
# have to place corresponding `LoadModule' lines at this location so the
# directives contained in it are actually available _before_ they are used.
# Statically compiled modules (those listed by `httpd -l') do not need
# to be loaded here.
#
# Example:
# LoadModule foo_module modules/mod_foo.so
#
Include conf.modules.d/*.conf

#
# If you wish httpd to run as a different user or group, you must run
# httpd as root initially and it will switch.
#
# User/Group: The name (or #number) of the user/group to run httpd as.
# It is usually good practice to create a dedicated user and group for
# running httpd, as with most system services.
#
User apache
Group apache

# 'Main' server configuration
#
# The directives in this section set up the values used by the 'main'
# server, which responds to any requests that aren't handled by a
# <VirtualHost> definition.  These values also provide defaults for
# any <VirtualHost> containers you may define later in the file.
#
# All of these directives may appear inside <VirtualHost> containers,
# in which case these default settings will be overridden for the
# virtual host being defined.
#

#
# ServerAdmin: Your address, where problems with the server should be
# e-mailed.  This address appears on some server-generated pages, such
# as error documents.  e.g. admin@your-domain.com
#
ServerAdmin root@localhost

#
# ServerName gives the name and port that the server uses to identify itself.
# This can often be determined automatically, but we recommend you specify
# it explicitly to prevent problems during startup.
#
# If your host doesn't have a registered DNS name, enter its IP address here.
#
#ServerName www.example.com:80

#
# Deny access to the entirety of your server's filesystem. You must
# explicitly permit access to web content directories in other
# <Directory> blocks below.
#
<Directory />
    AllowOverride none
    Require all denied
</Directory>

#
# Note that from this point forward you must specifically allow
# particular features to be enabled - so if something's not working as
# you might expect, make sure that you have specifically enabled it
# below.
#

#
# DocumentRoot: The directory out of which you will serve your
# documents. By default, all requests are taken from this directory, but
# symbolic links and aliases may be used to point to other locations.
#
DocumentRoot "/var/www/html"

#
# Relax access to content within /var/www.
#
<Directory "/var/www">
    AllowOverride None
    # Allow open access:
    Require all granted
</Directory>

# Further relax access to the default document root:
<Directory "/var/www/html">
    #
    # Possible values for the Options directive are "None", "All",
    # or any combination of:
    #   Indexes Includes FollowSymLinks SymLinksifOwnerMatch ExecCGI MultiViews
    #
    # Note that "MultiViews" must be named *explicitly* --- "Options All"
    # doesn't give it to you.
    #
    # The Options directive is both complicated and important.  Please see
    # http://httpd.apache.org/docs/2.4/mod/core.html#options
    # for more information.
    #
    Options Indexes FollowSymLinks

    #
    # AllowOverride controls what directives may be placed in .htaccess files.
    # It can be "All", "None", or any combination of the keywords:
    #   Options FileInfo AuthConfig Limit
    #
    AllowOverride None

    #
    # Controls who can get stuff from this server.
    #
    Require all granted
</Directory>

#
# DirectoryIndex: sets the file that Apache will serve if a directory
# is requested.
#
<IfModule dir_module>
    DirectoryIndex index.html
</IfModule>

#
# The following lines prevent .htaccess and .htpasswd files from being
# viewed by Web clients.
#
<Files ".ht*">
    Require all denied
</Files>

#
# ErrorLog: The location of the error log file.
# If you do not specify an ErrorLog directive within a <VirtualHost>
# container, error messages relating to that virtual host will be
# logged here.  If you *do* define an error logfile for a <VirtualHost>
# container, that host's errors will be logged there and not here.
#
ErrorLog "logs/error_log"

#
# LogLevel: Control the number of messages logged to the error_log.
# Possible values include: debug, info, notice, warn, error, crit,
# alert, emerg.
#
LogLevel warn

<IfModule log_config_module>
    #
    # The following directives define some format nicknames for use with
    # a CustomLog directive (see below).
    #
    LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined
    LogFormat "%h %l %u %t \"%r\" %>s %b" common

    <IfModule logio_module>
      # You need to enable mod_logio.c to use %I and %O
      LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\" %I %O" combinedio
    </IfModule>

    #
    # The location and format of the access logfile (Common Logfile Format).
    # If you do not define any access logfiles within a <VirtualHost>
    # container, they will be logged here.  Contrariwise, if you *do*
    # define per-<VirtualHost> access logfiles, transactions will be
    # logged therein and *not* in this file.
    #
    #CustomLog "logs/access_log" common

    #
    # If you prefer a logfile with access, agent, and referer information
    # (Combined Logfile Format) you can use the following directive.
    #
    CustomLog "logs/access_log" combined
</IfModule>

<IfModule alias_module>
    #
    # Redirect: Allows you to tell clients about documents that used to
    # exist in your server's namespace, but do not anymore. The client
    # will make a new request for the document at its new location.
    # Example:
    # Redirect permanent /foo http://www.example.com/bar

    #
    # Alias: Maps web paths into filesystem paths and is used to
    # access content that does not live under the DocumentRoot.
    # Example:
    # Alias /webpath /full/filesystem/path
    #
    # If you include a trailing / on /webpath then the server will
    # require it to be present in the URL.  You will also likely
    # need to provide a <Directory> section to allow access to
    # the filesystem path.

    #
    # ScriptAlias: This controls which directories contain server scripts.
    # ScriptAliases are essentially the same as Aliases, except that
    # documents in the target directory are treated as applications and
    # run by the server when requested rather than as documents sent to the
    # client.  The same rules about trailing "/" apply to ScriptAlias
    # directives as to Alias.
    #
    ScriptAlias /cgi-bin/ "/var/www/cgi-bin/"

</IfModule>

#
# "/var/www/cgi-bin" should be changed to whatever your ScriptAliased
# CGI directory exists, if you have that configured.
#
<Directory "/var/www/cgi-bin">
    AllowOverride None
    Options None
    Require all granted
</Directory>

<IfModule mime_module>
    #
    # TypesConfig points to the file containing the list of mappings from
    # filename extension to MIME-type.
    #
    TypesConfig /etc/mime.types

    #
    # AddType allows you to add to or override the MIME configuration
    # file specified in TypesConfig for specific file types.
    #
    #AddType application/x-gzip .tgz
    #
    # AddEncoding allows you to have certain browsers uncompress
    # information on the fly. Note: Not all browsers support this.
    #
    #AddEncoding x-compress .Z
    #AddEncoding x-gzip .gz .tgz
    #
    # If the AddEncoding directives above are commented-out, then you
    # probably should define those extensions to indicate media types:
    #
    AddType application/x-compress .Z
    AddType application/x-gzip .gz .tgz

    #
    # AddHandler allows you to map certain file extensions to "handlers":
    # actions unrelated to filetype. These can be either built into the server
    # or added with the Action directive (see below)
    #
    # To use CGI scripts outside of ScriptAliased directories:
    # (You will also need to add "ExecCGI" to the "Options" directive.)
    #
    #AddHandler cgi-script .cgi

    # For type maps (negotiated resources):
    #AddHandler type-map var

    #
    # Filters allow you to process content before it is sent to the client.
    #
    # To parse .shtml files for server-side includes (SSI):
    # (You will also need to add "Includes" to the "Options" directive.)
    #
    AddType text/html .shtml
    AddOutputFilter INCLUDES .shtml
</IfModule>

#
# Specify a default charset for all content served; this enables
# interpretation of all content as UTF-8 by default.  To use the
# default browser choice (ISO-8859-1), or to allow the META tags
# in HTML content to override this choice, comment out this
# directive:
#
AddDefaultCharset UTF-8

<IfModule mime_magic_module>
    #
    # The mod_mime_magic module allows the server to use various hints from the
    # contents of the file itself to determine its type.  The MIMEMagicFile
    # directive tells the module where the hint definitions are located.
    #
    MIMEMagicFile conf/magic
</IfModule>

#
# Customizable error responses come in three flavors:
# 1) plain text 2) local redirects 3) external redirects
#
# Some examples:
#ErrorDocument 500 "The server made a boo boo."
#ErrorDocument 404 /missing.html
#ErrorDocument 404 "/cgi-bin/missing_handler.pl"
#ErrorDocument 402 http://www.example.com/subscription_info.html
#

#
# EnableMMAP and EnableSendfile: On systems that support it,
# memory-mapping or the sendfile syscall may be used to deliver
# files.  This usually improves server performance, but must
# be turned off when serving from networked-mounted
# filesystems or if support for these functions is otherwise
# broken on your system.
# Defaults if commented: EnableMMAP On, EnableSendfile Off
#
#EnableMMAP off
EnableSendfile on

# Supplemental configuration
#
# Load config files in the "/etc/httpd/conf.d" directory, if any.
IncludeOptional conf.d/*.conf
</pre>



All these settings are defined in the <a href="https://httpd.apache.org/docs/2.4/mod/directives.html" target="_blank" rel="nofollow">Apache Directives Documentation</a>. 

Notice that in Apache, settings are referred to as <strong>Directives</strong>.

You should read this document in conjunction with the <a href="https://httpd.apache.org/docs/2.4/mod/quickreference.html" target="_blank" rel="nofollow">Directive Quick Reference Guide</a>, and <a href="https://httpd.apache.org/docs/2.4/mod/directive-dict.html" target="_blank" rel="nofollow">Dictionary dictionary</a>. 

You can also access them locally like this (if you have installed httpd-manual rpm):


<pre lang='bash'>
$ elinks http://localhost/manual/mod/directives.html
$ elinks http://localhost/manual/mod/directive-dict.html
$ elinks http://localhost/manual/mod/quickreference.html
</pre>




Let's take a look at some of these setting in turn:

<h2>The 'ServerRoot' setting</h2>
The <a href="https://httpd.apache.org/docs/2.4/mod/core.html#serverroot" target="_blank" rel="nofollow">ServerRoot</a> sets the location where all of Apache's configurations and logs are stored. Some of the files, e.g. logs are not actually sotred in this directory, instead there are symbolic links that are pointing to the actual locations, e.g.:

<pre>
[root@webserver httpd]# ll /etc/httpd
total 0
drwxr-xr-x. 2 root root  37 Mar  4 15:56 conf
drwxr-xr-x. 2 root root 135 Mar  4 15:56 conf.d
drwxr-xr-x. 2 root root 186 Mar  3 22:01 conf.modules.d
<strong>lrwxrwxrwx. 1 root root  19 Mar  3 22:01 logs -> ../../var/log/httpd
lrwxrwxrwx. 1 root root  29 Mar  3 22:01 modules -> ../../usr/lib64/httpd/modules
lrwxrwxrwx. 1 root root  10 Mar  3 22:01 run -> /run/httpd</strong>

</pre>


<h2>The 'Listen' Directive</h2>
What <a href="https://httpd.apache.org/docs/2.4/mod/mpm_common.html#listen" target="_blank" rel="nofollow">Listen</a> setting sets the port that the httpd deamon should be listening on. Here are some things to keep in mind when changing this setting to a non standard port:

<ul>
	<li>Use an available port - Don't change this to a port that's already in use by another daemon</li>
	<li>Open up necessory ports in firewalld</li>
	<li>Update SELinux settings to support the non standard port</li>
	<li>Need to specify the port in web browsers - Firefox and Chrome will automatically assume port 80/443 when accessing urls. So need to override this by explicitly specify port number in the url, e.g. http://mywebsite.com:3423</li>
</ul>

If you web server has multiple network interfaces, e.g. enp0s8, enp0s9,..etc, and you want to restrict web traffic from one interface, then you can do this by setting ip address in the listen directive, e.g.:


<pre>
Listen 12.34.56.78:80
</pre> 

Note: you can find another default listen directive for https, specified in:

<pre>
$ cat /etc/httpd/conf.d/ssl.conf | grep Listen
Listen 443 https
</pre>



<h2>The 'Include' setting</h2>
The <a href="https://httpd.apache.org/docs/2.4/mod/core.html#include">Include</a> loads in configurations from other locations. At the moment this setting is set to a relative path, <code>conf.modules.d/*.conf</code>. As a result Apache will assume that the corresponding absolute path is <code>{ServerRoot}/conf.modules.d/*.conf</code>, i.e. /etc/httpd/conf.modules.d/*.conf. 

If the Include Directive wildcard expression fails to match, then Apache will error out. If you want to suppress that error then you should <strong>IncludeOptional</strong> directive instead, covered further on. 

Speaking of <code>/etc/httpd/conf.modules.d/*.conf</code>, this contains config files which tells Apache which modules should be running when the the httpd daemon is running. You can confirm the correct modules are loaded by running:

<pre>
$ apachectl -M
</pre> 

Also you can view a full list of all available modules here:


<pre>
[root@webserver modules]# ll /usr/lib64/httpd/modules | head
total 2788
-rwxr-xr-x. 1 root root  11208 Oct 19 20:40 mod_access_compat.so
-rwxr-xr-x. 1 root root  11168 Oct 19 20:40 mod_actions.so
-rwxr-xr-x. 1 root root  15360 Oct 19 20:40 mod_alias.so
-rwxr-xr-x. 1 root root  11136 Oct 19 20:40 mod_allowmethods.so
-rwxr-xr-x. 1 root root  11088 Oct 19 20:40 mod_asis.so
-rwxr-xr-x. 1 root root  15328 Oct 19 20:40 mod_auth_basic.so
-rwxr-xr-x. 1 root root  36080 Oct 19 20:40 mod_auth_digest.so
.
.
.
...etc
</pre>

If there are any modules that you want to load in then you need to reference in the /etc/httpd/conf.modules.d directory.


<h2>The 'User' and 'Group' settings</h2>
When you start the httpd deamon, it ends up starting a single parent process followed by multiple child processes. For example:


<pre lang='bash'>
[root@webserver httpd]# systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
   Active: active (running) since Sun 2018-03-04 16:43:50 UTC; 4min 52s ago
     Docs: man:httpd(8)
           man:apachectl(8)
  Process: 5547 ExecStop=/bin/kill -WINCH ${MAINPID} (code=exited, status=0/SUCCESS)
 Main PID: 5552 (httpd)
   Status: "Total requests: 0; Current requests/sec: 0; Current traffic:   0 B/sec"
   CGroup: /system.slice/httpd.service
           ├─5552 /usr/sbin/httpd -DFOREGROUND
           ├─5553 /usr/sbin/httpd -DFOREGROUND
           ├─5554 /usr/sbin/httpd -DFOREGROUND
           ├─5555 /usr/sbin/httpd -DFOREGROUND
           ├─5556 /usr/sbin/httpd -DFOREGROUND
           ├─5557 /usr/sbin/httpd -DFOREGROUND
           └─5558 /usr/sbin/httpd -DFOREGROUND

Mar 04 16:43:50 webserver.local systemd[1]: Starting The Apache HTTP Server...
Mar 04 16:43:50 webserver.local systemd[1]: Started The Apache HTTP Server.
</pre>

Here it shows that the parent process's pid is '5552'. To see what the child processes are we do:


<pre>
[root@webserver httpd]# pstree -p -a 5552
httpd,5552 -DFOREGROUND
  ├─httpd,5553 -DFOREGROUND
  ├─httpd,5554 -DFOREGROUND
  ├─httpd,5555 -DFOREGROUND
  ├─httpd,5556 -DFOREGROUND
  ├─httpd,5557 -DFOREGROUND
  └─httpd,5558 -DFOREGROUND
</pre>

here we can see the each child process's (p)ids command-line (a)rguements. 



This setting specifies which username/group that the child processs will be run under. The parent process itself has to be owned by the user that started the httpd daemon, i.e. the root user. 


<pre lang='bash'>
[root@webserver httpd]# ps -ef f | grep httpd | grep -v grep
root      5552     1  0 16:43 ?        Ss     0:00 /usr/sbin/httpd -DFOREGROUND
vagrant   5553  5552  0 16:43 ?        S      0:00  \_ /usr/sbin/httpd -DFOREGROUND
vagrant   5554  5552  0 16:43 ?        S      0:00  \_ /usr/sbin/httpd -DFOREGROUND
vagrant   5555  5552  0 16:43 ?        S      0:00  \_ /usr/sbin/httpd -DFOREGROUND
vagrant   5556  5552  0 16:43 ?        S      0:00  \_ /usr/sbin/httpd -DFOREGROUND
vagrant   5557  5552  0 16:43 ?        S      0:00  \_ /usr/sbin/httpd -DFOREGROUND
vagrant   5558  5552  0 16:43 ?        S      0:00  \_ /usr/sbin/httpd -DFOREGROUND
</pre>


The root user is the only user that has the authority to start a deamon that listens on a port number between 1-1000.


<h2>The 'Directory' block Directive</h2>

Here's what the <a href="https://httpd.apache.org/docs/2.4/mod/core.html#directory">Directory</a> directive block setting looks like:

<pre lang="bash">
<Directory />
    AllowOverride none
    Require all denied
</Directory>
</pre>

Here the Directory tags specifies the scope at which the containing settings should be applied to. In this the scope is the very top level '/' and recursively down all child folders. This is a really broad setting, which in effect covers the whole CentOS machine. You can declare However further down the same file, we have:

<pre lang="bash">
<Directory "/var/www">
    AllowOverride None
    # Allow open access:
    Require all granted
</Directory>
</pre>

This time the scope is more targeted, /var/www. Basically what's happening here is that the settings that resides in a more targeted blocks overrides (where there are conflicts) the broader directory block's settings. Let's look at the specs inside the directory block:

<ul>
	<li><a href="https://httpd.apache.org/docs/2.4/mod/core.html#allowoverride" target="_blank" rel="nofollow">AllowOverride</a> - This controls what features of the .htaccess file to allow (if any).</li>
	<li><a href="https://httpd.apache.org/docs/2.4/mod/mod_authz_core.html#require" target="_blank" rel="nofollow">Require</a> - Controls access to resources</li>
</ul>


Here's another example:


<pre lang="bash">
<Directory "/var/www/html">
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>
</pre>

Here we have the <a href="https://httpd.apache.org/docs/2.4/mod/core.html#options" target="_blank" rel="nofollow">Options directive</a>, which let's you enable a number of features for the given directory. In this case we have:

<ul>
	<li><strong>Indexes</strong> - If a user requests for the index.html file, and that doesn't exist then Apache will instead display a plain table (that looks <a href="http://download.fedoraproject.org/pub/epel/7" target="_blank" rel="nofollow">a bit like this</a>) listing all the available files to choose from. </li>
	<li><strong>FollowSymLinks</strong> - This basically let's you place symlinks in the given directory to point to files other location, such as in the /etc folder. Note, you don't need to do any additional SELinux work to get this working.</li>
</ul>





 



<h2>The 'DocumentRoot' setting</h2>
This is where the actual web content will reside, e.g. *.html, *.css, *.php files .....etc. By default this is set to <code>/var/www/html</code>. One thing to bear in mind is that if you want to change this, then you need to configure SELinux to support whatever the new location is. 




<h2>The 'IfModule' block Directive</h2>
This <a href="https://httpd.apache.org/docs/2.4/mod/core.html#ifmodule" target="_blank" rel="nofollow">IfModule</a> block comes into play if the specified module is present. Here's an example:

<pre lang='bash'>
<IfModule dir_module>
    DirectoryIndex index.html
</IfModule>
</pre>

This checks to see if the dir_module is present, you can check this manually using the apachectl command:


<pre lang='bash'>
[root@webserver httpd]# apachectl -M | grep dir
 dir_module (shared)
 userdir_module (shared)
</pre>

Here we can see dir_module is enabled. This means that the <a href="https://httpd.apache.org/docs/2.4/mod/mod_dir.html#directoryindex" target="_blank" rel="nofollow">DirectoryIndex</a> setting will come into effect. 


This basically means that if the url doesn't specify a particular file, then Apache will default to serving up index.html as the default, i.e. if you request for www.codingbee.net then Apache will assume and serve www.codingbee.net/index.html. 



<h2>The 'Files' block Directive</h2>

The <a href="https://httpd.apache.org/docs/2.4/mod/core.html#files" target="_blank" rel="nofollow">Files directive</a> is similar to the Directive but controls settings at the file level, here's an example:


<pre lang='bash'>
<Files ".ht*">
    Require all denied
</Files>
</pre>

In this case, this will prevent website visitors from accessing files such as .htaccess, irrespective of which directory it's in. 

<h2>The ErrorLog directive</h2>
The <a href="https://httpd.apache.org/docs/2.4/mod/core.html#errorlog" target="_blank" rel="nofollow">ErrorLog directive</a> let's you specify set where you want your error logs to be written to. In this case it is:



<pre>ErrorLog "logs/error_log"</pre>


Since this is specified as a relative path, Apache will assume it is relative to the ServerRoot location, i.e. Apache will assume the above corresponds to /etc/httpd/logs/error_log. 

However since /etc/httpd/logs is a symbolic link:

<pre>
[root@webserver httpd]# ll /etc/httpd/logs
lrwxrwxrwx. 1 root root 19 Mar  3 22:01 /etc/httpd/logs -> ../../var/log/httpd
</pre>

It means that the actual location where the logs are stored is:


<pre>
[root@webserver httpd]# file /var/log/httpd/error_log
/var/log/httpd/error_log: ASCII text
</pre>


<h2>The LogLevel directive</h2>

The <a href="https://httpd.apache.org/docs/2.4/mod/core.html#loglevel" target="_blank" rel="nofollow">LogLevel directive</a> sets the verbosity of the logging. 

<h2>The LogFormat directive</h2>
The <a href="https://httpd.apache.org/docs/2.4/mod/mod_log_config.html#logformat" target="_blank" rel="nofollow">LogFormat directive</a> let's you specify the format of your log entries. Optionally you can then give this format a nickname, which you can then reference, for example:


<pre>
LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined
</pre>

Here we assigned the this format the nickname 'combined'. We can then use this nickname to reference this format in other config settings, for example in the CustomLog directive. 


<h2>The CustomLog directive</h2>
The <a href="https://httpd.apache.org/docs/2.4/mod/mod_log_config.html#customlog" target="_blank" rel="nofollow">CustomLog directive</a> let's you specify a custom location for sending access related log entries to. Here's an example:

<pre>
CustomLog "logs/access_log" combined
</pre>

Again this is a relative, which Apache resolves (using the ServerRoot setting) to 

<pre>
/etc/httpd/logs/access_log
</pre>

Which in turn ultimately points to <code>/var/log/httpd/access_log</code> 





<h2>The ErrorLog directive</h2>
The <a href="https://httpd.apache.org/docs/2.4/mod/core.html#errorlog" target="_blank" rel="nofollow">ErrorLog directive</a> is similar to the CustomLog directive, but this time for Error logs.



<h2>The IncludeOptional directive</h2>
The <a href="https://httpd.apache.org/docs/2.4/mod/core.html#includeoptional" target="_blank" rel="nofollow">IncludeOptional directive</a> essentially pulls in other configs. These settings can be (and is often) used to override the configurations in this file. 

<pre>
IncludeOptional conf.d/*.conf
</pre>

Again this is a relative path, which Apache will assume it is relative to the ServerRoot location, i.e. Apache will assume the above corresponds to all /etc/httpd/conf.d/*.conf files. 








<h2>Overrides, overrides, and more Overrides</h2>

In Apache, the configurations can be stored in multiple places. In fact in Apache duplicate directives are specified with the intention of more targetted directives overriding the duplciates. The penultimate column in the <a href="https://httpd.apache.org/docs/2.4/mod/quickreference.html" target="_blank" rel="nofollow">Apache Quick Reference</a> guide shows where certain directives can be declared. Here's the order of precedence:

<ol>
	<li>.htaccess - Directive setting here overrides everything (as long as the .htaccess feature has been enabled)</li>
	<li>Directory - settings specified in the Directory block will take precedence the more targetted it is.
 Also those that are specified within vhost config files takes precedence over those at the top level server config file.</li>
	<li>vhosts - directive specified in vhosts takes precedance over server configs</li>
	<li>server config - takes lowest priority over duplicate directives specified elsewhere</li>

</ol>


[post-content post_name=rhsca-quiz] 

[accordion]
[toggle title="Where is the main apache file located?"]
- /etc/httpd/conf/httpd.conf
- /etc/httpd/conf.d/ssl.conf
[/toggle]
[toggle title="Where are the main help info located with apache configurations?"]
$ elinks http://localhost/manual/mod/directives.html
[/toggle]
[toggle title="What is the 'ServerRoot' directive for?"]
Specifies location of all the main apache configurations and logs. Other settings may refer to this to resolve to full paths when relative paths specified
[/toggle]
[toggle title="What is the 'ServerName' directive for?"]
This sets the url the the web server identifies itself with. The ServerName is particularly important when setting up virtual hosts.   
[/toggle]
[toggle title="What is the 'Listen' setting for?"]
Specifies what port number port number to listen to. Can also apply interface based restriction. 
[/toggle]
[toggle title="What is the 'include' setting for?"]
Specifies location of other locations to load in configs from
[/toggle]
[toggle title="What is the the ‘User’ and ‘Group’ settings setting for?"]
what user/group apache worker process should be run under. 
[/toggle]
[toggle title="What is the 'Directive' block setting for?"]
his let's you specify settings recursively for a particular folder. E.g. '/' would mean the root folder, '/' and everything below it. Note you can have successive directive blocks can override some of the earlier directive block settings. 
[/toggle]
[toggle title="What is the 'AllowOverride' setting for?"]
This controls what features can be allowed in the .htaccess file. 
[/toggle]
[toggle title="What is the 'Options' setting for?"]
This lets you specify what features to enable.
[/toggle]
[toggle title="What are common Options, and what do they do?"]
Indexes - let's you use index.html, otherwise default to a plain table 
FollowSymLinks - let's you follow symlinks
[/toggle]
[toggle title="What is the 'DocumenRoot' setting for?"]
This specifies the folder that stores all the web content files. 
[/toggle]
[toggle title="What is the 'IfModule' block setting for?"]
This block content only takes effect IF the specified module is enabled. 
[/toggle]
[toggle title="What is the 'files' block setting for?"]
This block affects particular file. Usually the files are specified using wildcards. It's often used to prevent people from access accessing the .htaccess file. 
[/toggle]
[toggle title="What is the 'LogLevel' setting for?"]
This sets logging verbosity level.
[/toggle]
[toggle title="What is the 'LogFormat' setting for?"]
This let's you define a log format and then label the format, e.g. the default nickname is 'combined'
[/toggle]
[toggle title="What is the 'CustomLog' setting for?"]
This lets you set the location to store access logs, and also you can reference a log format label. 
[/toggle]
[toggle title="What is the 'ErrorLog' setting for?"]
This sets where you want to store your error logs. 
[/toggle]
[toggle title="What is the 'IncludeOptional' setting for?"]
This is used to 'source' configs stored elsewhere, this is most commonly used to load in conf.d/*.conf files. 
[/toggle]
[toggle title="What is the command to list all worker/child processes?"]
$ ps -ef f | grep httpd | grep -v grep
[/toggle]
[toggle title="What is the command to list all loaded apache modules?"]
$ httpd -M
[/toggle]
[toggle title="What is the command to config test httpd config files?"]
$ httpd -t
[/toggle]
[/accordion]