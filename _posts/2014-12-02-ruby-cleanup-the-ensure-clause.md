---
ID: 202
post_title: 'Ruby &#8211; Cleanup (the &#8220;ensure&#8221; clause)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-cleanup-the-ensure-clause
published: true
post_date: 2014-12-02 00:00:00
---
Now let's say in a given method, you open a file. 

At the end of the method, this file has to then be closed. Within a method, there are three places where you want to include the "file.close" code:

<ul>
	<li>Just before the end of the method, providing method runs successfully. </li>
	<li>Via the "raise" keyword if something unexpected happens</li>
	<li>inside the rescue clause. </li>
<ul>

i.e.

<pre>
def method_name
  a_file = File.open("testfile.txt")   # this has to be closed no matter how the method ends. 
  # ..
  # ..
  raise CostomError "custom message" if custom_method?   # here we can close the file. 
  # ..
  # ..
  # we can close the file here, just before the method ends successfully.   
  true
rescue
  # ..
  # we can also close the file here. 
  false
end



</pre>


This will result in code duplication and can get messy. That's why there is a better approach, and that is to use the "ensure" clause. The code in the ensure clause get run reqardless of whether the method ran successfully or it encountered an error.




<pre>
def method_name
  a_file = File.open("testfile.txt")   # this has to be closed no matter how the method ends. 
  # ..
  # ..
  raise CostomError "custom message" if custom_method?   
  # ..
  # ..
  true
rescue
  # ..
  # ..
  false
ensure
  a_file.close if a_file
end

</pre>

Now we only have one place that will close the file. We also appended an if-statement just to cover the possible scenarion that File.open(...) itself fails. 

Also, don't put any return statement in the ensure clause, as that could end up over-riding any initial returns of "false". 

Another thing you can do is place an "else" clause between the "rescue" and "ensure" clause. The code in this block only gets run, if no errors have been encountered, here's an exmaple:


<pre>
def method_name
  a_file = File.open("testfile.txt")   # this has to be closed no matter how the method ends. 
  # ..
  # ..
  raise CostomError "custom message" if custom_method?   
  # ..
  # ..
  true
rescue
  # ..
  # ..
  false
else
  puts "woohoo no error messages encountered."
ensure
  a_file.close if a_file
end

note, the ensure clause get's executed in all cases, regardless of whether there is an else clause or not.