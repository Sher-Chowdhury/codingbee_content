---
ID: 203
post_title: 'Ruby &#8211; Retrying if error encountered the first time'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-retrying-if-error-encountered-the-first-time
published: true
post_date: 2014-12-02 00:00:00
---
In some cases you may want to re-attempt a block of code, if it encounters a failure for the first time. 

A typical example would be if you were trying to do a SOAP/REST request to a web service that is known to be occasionally intermittent. 


One way to handle this is with the use of loops:


<pre>
def soap_request
  1.upto (3) do |attempt|
    begin 
      retrieve_soap_object = API.request(www.WorldWeather.com)
      break   # this exit the re-attempting loop as soon as there is a success. 
    rescue  RunetimeError => error_object 
      puts error_object.message
      if attempt == 3
        puts "request failed."
        raise
      end  # end of if-statment check. 
    end    # end of begin statement
  end      # end of 1..3 loop 
end   # end of method. 

</pre>

However there is a special syntax you can use just for this purpose, which is using the "retry" statement:


<pre>
def soap_request
  retrieve_soap_object = API.request(www.WorldWeather.com)
  # ..
  # ..
  # ..  
rescue  RunetimeError => error_object 
  attempts ||= 0       # this creates variable if it doesn't exist yet. 
  attempts += 1
  if attempts < 3
    puts error_object.message + ". Reattempting SOAP/REST request again."
    retry                      # this instructs ruby to return to the first line of the method again.
                               # i.e. it will try to recreate retrieve_soap_object again.   
  else
    puts "Request failed."
    raise
  end      # end of if-statement
end   # end of method. 

</pre>

As you can see, this approach has a lot less nesting and is easer to read.