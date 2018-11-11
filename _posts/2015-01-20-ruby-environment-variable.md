---
ID: 272
post_title: 'Ruby &#8211; Environment Variable'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-environment-variable
published: true
post_date: 2015-01-20 00:00:00
---
You can read all the environment variables via the irb using the "ENV" command:


<pre>
irb(main):231:0* puts ENV.sort
COLUMNS
188
HISTCONTROL
ignoredups
HISTSIZE
1000
HOME
/root
HOSTNAME
puppetmaster.local
LANG
en_GB.UTF-8
.
.
...etc
</pre>


Here the keys are in capital and the values are in string. 

Alternatively we could use "pretty print" (pp) which comes with the Ruby standard library:


<pre>
$ irb
irb(main):001:0> pp ENV.sort
NoMethodError: undefined method `pp' for main:Object
        from (irb):1
        from /usr/bin/irb:12:in `<main>'
</pre>


This didn't work because pp needs be required first:

<pre>
irb(main):006:0* require 'pp'
=> true
irb(main):007:0> pp ENV.sort
[["COLUMNS", "188"],
 ["HISTCONTROL", "ignoredups"],
 ["HISTSIZE", "1000"],
 ["HOME", "/root"],
 ["HOSTNAME", "puppetmaster.local"],
 ["LANG", "en_GB.UTF-8"],
 ["LESSOPEN", "||/usr/bin/lesspipe.sh %s"],
 ["LINES", "58"],
 ["LOGNAME", "root"],
.
.
...etc.
</pre>

Another option is to exit out of IRB and then install a gem called "awesome_print" and then use that tool instead:


<pre>
$ gem install awesome_print
Fetching: awesome_print-1.6.1.gem (100%)
Successfully installed awesome_print-1.6.1
Parsing documentation for awesome_print-1.6.1
Installing ri documentation for awesome_print-1.6.1
1 gem installed
$
$
$
irb(main):080:0* awesome_print ENV
{
               "XDG_SESSION_ID" => "2637",
                     "HOSTNAME" => "puppetmaster.local",
       "SELINUX_ROLE_REQUESTED" => "",
                         "TERM" => "xterm",
                        "SHELL" => "/bin/bash",
                     "HISTSIZE" => "1000",
                   "SSH_CLIENT" => "192.168.50.1 57615 22",
    "SELINUX_USE_CURRENT_RANGE" => "",
                      "SSH_TTY" => "/dev/pts/3",
                         "USER" => "root",
.
.
.
}
=> nil
</pre>

You can also create environment variables using the following syntax:

<pre>
ENV['key'] = 'value'
</pre>

For example:

<pre>
[root@puppetmaster pod_env]# irb
1.9.3-p551 :002 > require 'awesome_print'
 => true 
1.9.3-p551 :003 > awesome_print ENV
{
          "rvm_bin_path" => "/usr/local/rvm/bin",
              "HOSTNAME" => "puppetmaster.ordsvy.gov.uk",
              "GEM_HOME" => "/usr/local/rvm/gems/ruby-1.9.3-p551",
                 "SHELL" => "/bin/bash",
                  "TERM" => "xterm",
              "HISTSIZE" => "1000",
                 "IRBRC" => "/usr/local/rvm/rubies/ruby-1.9.3-p551/.irbrc",
                 "QTDIR" => "/usr/lib64/qt-3.3",
                "OLDPWD" => "/git_source/puppet/environments/dev_virtualbox_v2/modules",
          "MY_RUBY_HOME" => "/usr/local/rvm/rubies/ruby-1.9.3-p551",
                 "QTINC" => "/usr/lib64/qt-3.3/include",
                  "USER" => "root",
             "LS_COLORS" => "rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=01;05;37;41:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arj=01;31:*.taz=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.zip=01;31:*.z=01;31:*.Z=01;31:*.dz=01;31:*.gz=01;31:*.lz=01;31:*.xz=01;31:*.bz2=01;31:*.tbz=01;31:*.tbz2=01;31:*.bz=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.rar=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.jpg=01;35:*.jpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.axv=01;35:*.anx=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=01;36:*.au=01;36:*.flac=01;36:*.mid=01;36:*.midi=01;36:*.mka=01;36:*.mp3=01;36:*.mpc=01;36:*.ogg=01;36:*.ra=01;36:*.wav=01;36:*.axa=01;36:*.oga=01;36:*.spx=01;36:*.xspf=01;36:",
          "_system_type" => "Linux",
              "rvm_path" => "/usr/local/rvm",
            "rvm_prefix" => "/usr/local",
                  "MAIL" => "/var/spool/mail/root",
                  "PATH" => "/usr/local/rvm/gems/ruby-1.9.3-p551/bin:/usr/local/rvm/gems/ruby-1.9.3-p551@global/bin:/usr/local/rvm/rubies/ruby-1.9.3-p551/bin:/usr/lib64/qt-3.3/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/usr/local/rvm/bin:/root/bin",
                   "PWD" => "/git_source/puppet/environments/dev_virtualbox_v2/modules/pod_env",
                  "LANG" => "en_US.UTF-8",
          "_system_arch" => "x86_64",
       "_system_version" => "6",
           "SSH_ASKPASS" => "/usr/libexec/openssh/gnome-ssh-askpass",
           "HISTCONTROL" => "ignoredups",
           "rvm_version" => "1.26.3 (latest)",
                 "SHLVL" => "1",
                  "HOME" => "/root",
               "LOGNAME" => "root",
                 "QTLIB" => "/usr/lib64/qt-3.3/lib",
               "CVS_RSH" => "ssh",
              "GEM_PATH" => "/usr/local/rvm/gems/ruby-1.9.3-p551:/usr/local/rvm/gems/ruby-1.9.3-p551@global",
              "LESSOPEN" => "|/usr/bin/lesspipe.sh %s",
               "DISPLAY" => ":0.0",
          "RUBY_VERSION" => "ruby-1.9.3-p551",
          "_system_name" => "RedHat",
    "G_BROKEN_FILENAMES" => "1",
            "XAUTHORITY" => "/root/.xauthZ90kSk",
                     "_" => "/usr/local/rvm/rubies/ruby-1.9.3-p551/bin/irb",
                 "LINES" => "60",
               "COLUMNS" => "237"
}
 => nil 
1.9.3-p551 :004 > puts ENV['EDITOR'] 

 => nil 
1.9.3-p551 :005 > ENV['EDITOR'] = 'gedit' 
 => "gedit" 
1.9.3-p551 :006 > puts ENV['EDITOR'] 
gedit
 => nil 
1.9.3-p551 :007 > 
1.9.3-p551 :007 > awesome_print ENV
{
          "rvm_bin_path" => "/usr/local/rvm/bin",
              "HOSTNAME" => "puppetmaster.ordsvy.gov.uk",
              "GEM_HOME" => "/usr/local/rvm/gems/ruby-1.9.3-p551",
                 "SHELL" => "/bin/bash",
                  "TERM" => "xterm",
              "HISTSIZE" => "1000",
                 "IRBRC" => "/usr/local/rvm/rubies/ruby-1.9.3-p551/.irbrc",
                 "QTDIR" => "/usr/lib64/qt-3.3",
                "OLDPWD" => "/git_source/puppet/environments/dev_virtualbox_v2/modules",
          "MY_RUBY_HOME" => "/usr/local/rvm/rubies/ruby-1.9.3-p551",
                 "QTINC" => "/usr/lib64/qt-3.3/include",
                  "USER" => "root",
             "LS_COLORS" => "rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=01;05;37;41:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arj=01;31:*.taz=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.zip=01;31:*.z=01;31:*.Z=01;31:*.dz=01;31:*.gz=01;31:*.lz=01;31:*.xz=01;31:*.bz2=01;31:*.tbz=01;31:*.tbz2=01;31:*.bz=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.rar=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.jpg=01;35:*.jpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.axv=01;35:*.anx=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=01;36:*.au=01;36:*.flac=01;36:*.mid=01;36:*.midi=01;36:*.mka=01;36:*.mp3=01;36:*.mpc=01;36:*.ogg=01;36:*.ra=01;36:*.wav=01;36:*.axa=01;36:*.oga=01;36:*.spx=01;36:*.xspf=01;36:",
          "_system_type" => "Linux",
              "rvm_path" => "/usr/local/rvm",
            "rvm_prefix" => "/usr/local",
                  "MAIL" => "/var/spool/mail/root",
                  "PATH" => "/usr/local/rvm/gems/ruby-1.9.3-p551/bin:/usr/local/rvm/gems/ruby-1.9.3-p551@global/bin:/usr/local/rvm/rubies/ruby-1.9.3-p551/bin:/usr/lib64/qt-3.3/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/usr/local/rvm/bin:/root/bin",
                   "PWD" => "/git_source/puppet/environments/dev_virtualbox_v2/modules/pod_env",
                  "LANG" => "en_US.UTF-8",
          "_system_arch" => "x86_64",
       "_system_version" => "6",
           "SSH_ASKPASS" => "/usr/libexec/openssh/gnome-ssh-askpass",
           "HISTCONTROL" => "ignoredups",
           "rvm_version" => "1.26.3 (latest)",
                 "SHLVL" => "1",
                  "HOME" => "/root",
               "LOGNAME" => "root",
                 "QTLIB" => "/usr/lib64/qt-3.3/lib",
               "CVS_RSH" => "ssh",
              "GEM_PATH" => "/usr/local/rvm/gems/ruby-1.9.3-p551:/usr/local/rvm/gems/ruby-1.9.3-p551@global",
              "LESSOPEN" => "|/usr/bin/lesspipe.sh %s",
               "DISPLAY" => ":0.0",
          "RUBY_VERSION" => "ruby-1.9.3-p551",
          "_system_name" => "RedHat",
    "G_BROKEN_FILENAMES" => "1",
            "XAUTHORITY" => "/root/.xauthZ90kSk",
                     "_" => "/usr/local/rvm/rubies/ruby-1.9.3-p551/bin/irb",
                 "LINES" => "60",
               "COLUMNS" => "237",
                "EDITOR" => "gedit"
}
 => nil 
1.9.3-p551 :008 > 
</pre>

Note, here that this environment variable only lasts for as long as the irb session. As soon as you restart irb, it disappears again.