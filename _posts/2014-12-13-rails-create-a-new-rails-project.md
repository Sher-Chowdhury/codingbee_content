---
ID: 220
post_title: 'Rails &#8211; Create a new rails project'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/rails/rails-create-a-new-rails-project
published: true
post_date: 2014-12-13 00:00:00
---
To create a new rails project on a windows machine, you need to take the following steps: 


1. start powershell terminal

2. create a folder that will house all your rails project

<pre>
PS C:\> mkdir RailsProjects


    Directory: C:\


Mode                LastWriteTime     Length Name
----                -------------     ------ ----
d----        13/12/2014     15:57            RailsProjects

PS C:\>
</pre>

3. cd into that directory

<pre>
PS C:\> cd .\RailsProjects
PS C:\RailsProjects>
</pre>

4. Next we have to set up this folder as our main folder that will house all our rails:


<pre>

PS C:\RailsProjects> gem install rails
Successfully installed rails-4.1.8
Parsing documentation for rails-4.1.8
Done installing documentation for rails after 2 seconds
1 gem installed
PS C:\RailsProjects> gem install rails

</pre>


5. Now run the following:
Now run:


<pre>
rails new {rails-project-name}
</pre>

For, example, here I am creating a rails project called "My-First-Rails-Project":

<pre>
PS C:\RailsProjects> rails new My-First-Rails-Project
DL is deprecated, please use Fiddle
      create
      create  README.rdoc
      create  Rakefile
      create  config.ru
      create  .gitignore
      create  Gemfile
      create  app
      create  app/assets/javascripts/application.js
      create  app/assets/stylesheets/application.css
      create  app/controllers/application_controller.rb
      create  app/helpers/application_helper.rb
      create  app/views/layouts/application.html.erb
      create  app/assets/images/.keep
      create  app/mailers/.keep
      create  app/models/.keep
      create  app/controllers/concerns/.keep
      create  app/models/concerns/.keep
      create  bin
      create  bin/bundle
      create  bin/rails
      create  bin/rake
      create  config
      create  config/routes.rb
      create  config/application.rb
      create  config/environment.rb
      create  config/secrets.yml
      create  config/environments
      create  config/environments/development.rb
      create  config/environments/production.rb
      create  config/environments/test.rb
      create  config/initializers
      create  config/initializers/assets.rb
      create  config/initializers/backtrace_silencers.rb
      create  config/initializers/cookies_serializer.rb
      create  config/initializers/filter_parameter_logging.rb
      create  config/initializers/inflections.rb
      create  config/initializers/mime_types.rb
      create  config/initializers/session_store.rb
      create  config/initializers/wrap_parameters.rb
      create  config/locales
      create  config/locales/en.yml
      create  config/boot.rb
      create  config/database.yml
      create  db
      create  db/seeds.rb
      create  lib
      create  lib/tasks
      create  lib/tasks/.keep
      create  lib/assets
      create  lib/assets/.keep
      create  log
      create  log/.keep
      create  public
      create  public/404.html
      create  public/422.html
      create  public/500.html
      create  public/favicon.ico
      create  public/robots.txt
      create  test/fixtures
      create  test/fixtures/.keep
      create  test/controllers
      create  test/controllers/.keep
      create  test/mailers
      create  test/mailers/.keep
      create  test/models
      create  test/models/.keep
      create  test/helpers
      create  test/helpers/.keep
      create  test/integration
      create  test/integration/.keep
      create  test/test_helper.rb
      create  tmp/cache
      create  tmp/cache/assets
      create  vendor/assets/javascripts
      create  vendor/assets/javascripts/.keep
      create  vendor/assets/stylesheets
      create  vendor/assets/stylesheets/.keep
         run  bundle install
DL is deprecated, please use Fiddle
Fetching gem metadata from https://rubygems.org/..........
Resolving dependencies...
Using rake 10.4.2
Using i18n 0.6.11
Using json 1.8.1
Using minitest 5.5.0
Using thread_safe 0.3.4
Using tzinfo 1.2.2
Using activesupport 4.1.8
Using builder 3.2.2
Using erubis 2.7.0
Using actionview 4.1.8
Using rack 1.5.2
Using rack-test 0.6.2
Using actionpack 4.1.8
Using mime-types 2.4.3
Using mail 2.6.3
Using actionmailer 4.1.8
Using activemodel 4.1.8
Using arel 5.0.1.20140414130214
Using activerecord 4.1.8
Using bundler 1.7.7
Using coffee-script-source 1.8.0
Using execjs 2.2.2
Using coffee-script 2.3.0
Using thor 0.19.1
Using railties 4.1.8
Using coffee-rails 4.0.1
Using hike 1.2.3
Using multi_json 1.10.1
Using jbuilder 2.2.5
Using jquery-rails 3.1.2
Using tilt 1.4.1
Using sprockets 2.12.3
Using sprockets-rails 2.2.2
Using rails 4.1.8
Using rdoc 4.2.0
Using sass 3.2.19
Using sass-rails 4.0.5
Using sdoc 0.4.1
Using sqlite3 1.3.10
Using turbolinks 2.5.3
Using tzinfo-data 1.2014.10
Using uglifier 2.6.0
Your bundle is complete!
Use `bundle show [gemname]` to see where a bundled gem is installed.
PS C:\RailsProjects>   
</pre>    


This creates the folder for the rails project, of the same name:

<pre>
PS C:\RailsProjects> ls


    Directory: C:\RailsProjects


Mode                LastWriteTime     Length Name
----                -------------     ------ ----
d----        13/12/2014     17:18            My-First-Rails-Project


PS C:\RailsProjects>
</pre>


At this point the rails project has now been created and is housed inside this folder. 
 

6. Now let's check the contents of the new rails project folder. First cd into it, then view it's contents:

<pre>
PS C:\RailsProjects>
PS C:\RailsProjects> cd .\My-First-Rails-Project
PS C:\RailsProjects\My-First-Rails-Project> ls


    Directory: C:\RailsProjects\My-First-Rails-Project


Mode                LastWriteTime     Length Name
----                -------------     ------ ----
d----        13/12/2014     17:17            app
d----        13/12/2014     17:17            bin
d----        13/12/2014     17:17            config
d----        13/12/2014     17:17            db
d----        13/12/2014     17:17            lib
d----        13/12/2014     17:17            log
d----        13/12/2014     17:17            public
d----        13/12/2014     17:17            test
d----        13/12/2014     17:17            tmp
d----        13/12/2014     17:17            vendor
-a---        13/12/2014     17:17        466 .gitignore
-a---        13/12/2014     17:17        154 config.ru
-a---        13/12/2014     17:17       1288 Gemfile
-a---        13/12/2014     17:18       2836 Gemfile.lock
-a---        13/12/2014     17:17        249 Rakefile
-a---        13/12/2014     17:17        478 README.rdoc


PS C:\RailsProjects\My-First-Rails-Project>
</pre>

You should then see contents similar to this. 

7. While in this folder, we can now check whether the rails project is set up correctly by seeing if we can start up the rails web server, which is done like this:  

<pre>
PS C:\RailsProjects\My-First-Rails-Project> rails server
=> Booting WEBrick
=> Rails 4.1.8 application starting in development on http://0.0.0.0:3000
=> Run `rails server -h` for more startup options
=> Notice: server is listening on all interfaces (0.0.0.0). Consider using 127.0.0.1 (--binding option)
=> Ctrl-C to shutdown server
[2014-12-13 17:30:35] INFO  WEBrick 1.3.1
[2014-12-13 17:30:35] INFO  ruby 2.1.5 (2014-11-13) [i386-mingw32]
[2014-12-13 17:30:35] INFO  WEBrick::HTTPServer#start: pid=8216 port=3000


Started GET "/" for 127.0.0.1 at 2014-12-13 17:30:36 +0000
Processing by Rails::WelcomeController#index as HTML
  Rendered C:/RailsInstaller/Ruby2.1.0/lib/ruby/gems/2.1.0/gems/railties-4.1.8/lib/rails/templates/rails/welcome/index.html.erb (13.0ms)
Completed 200 OK in 122ms (Views: 70.0ms | ActiveRecord: 0.0ms)


Started GET "/" for 127.0.0.1 at 2014-12-13 17:31:12 +0000
Processing by Rails::WelcomeController#index as HTML
  Rendered C:/RailsInstaller/Ruby2.1.0/lib/ruby/gems/2.1.0/gems/railties-4.1.8/lib/rails/templates/rails/welcome/index.html.erb (0.0ms)
Completed 200 OK in 10ms (Views: 9.0ms | ActiveRecord: 0.0ms)
</pre>
Note, at this point, you can't enter any new commands into the terminal while the rails web-server is running.

9. Now open up a web browser, and go to:
<pre>
http://localhost:3000
</pre>

<a href="http://codingbee.net/wp-content/uploads/2014/12/rails-web-server.png"><img src="http://codingbee.net/wp-content/uploads/2014/12/rails-web-server.png" alt="" width="923" height="560" class="alignnone size-full wp-image-2233" /></a>


10. If you see this page, then it means that you have successfully set up a blank rails project, which you can now start using to build your project. 

11. You can now go ahead and stop the rail server by typing ctrl+c in the terminal. This will return you back to the command prompt.