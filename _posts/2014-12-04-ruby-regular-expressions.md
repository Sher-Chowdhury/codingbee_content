---
ID: 215
post_title: 'Ruby &#8211; Regular Expressions'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/ruby/ruby-regular-expressions
published: true
post_date: 2014-12-04 00:00:00
---
In ruby, regular expressions are instantiated from the "<a href="http://ruby-doc.org/core-2.1.1/Regexp.html">Regexp</a>" class.  

The Rubymine ide has a very useful way to check any regexs that you have written. There's also an online <a href="http://rubular.com/">ruby regex checker called rubular</a>. 


You can write regular expressions in the following 2 ways:

<pre>/regular-expression/        # i.e. encased in /.../
%r(regular-exression)       # i.e. contained inside %r(...)</pre>

In Ruby, regex is commonly used to find matches in a string, and to test for a regex match, you need to use the "=~" operator:

<pre>
PS C:\Temp\irb> irb
irb(main):001:0> "hello world" =~ /el/
=> 1
irb(main):002:0> "hello world" =~ /elx/
=> nil
irb(main):003:0> /el/ =~ "hello world"               # it also works the other way round. 
=> 1
</pre>

As you can see, the return value of "1"  means a match has been found, otherwise it is nil. you can also check for zero matches using "!~"


<pre>
irb(main):004:0> "hello world" !~ /elx/
=> true
irb(main):005:0> "hello world" !~ /el/
=> false
irb(main):006:0>
</pre>

You can also capture match into an object. This is possible because the rexexp class has a "match" method for this purpose:

<pre>

irb(main):015:0* matches = /(\d+):(\d+)/.match("The current time is 15:45am")
=> #<MatchData "15:45" 1:"15" 2:"45">

</pre>

In this example, we are actually doing 3 regex matches with a single regex statement. This is possible by the use of round-brackets in our regex statement. The results of each match has been captured into the "matches" variable.

The regex we used is /(\d+):(\d+)/, lets look at this in parts:

- (\d+) - this means find a match of 1 or more digit. This will only return the first match. It captures the first match, and then stores it into the matches variable. 
- (\d+) - This does same as before, but looks for the 2nd match. 
- \d+:\d+ - the final match is done by removing all the brackets to find a whole match. 

Hence in the above example we go a single whole match, followed by 2 sub matches. 

Now we can output various parts of the matches (including non-matches) like this:


<pre>
irb(main):015:0* matches = /(\d+):(\d+)/.match("The current time is 15:45am")
=> #<MatchData "15:45" 1:"15" 2:"45">
irb(main):016:0> matches.class
=> MatchData
irb(main):017:0> matches.pre_match      # sub-string leading up to the first match.
=> "The current time is "                
irb(main):018:0> matches.post_match    # sub-string after the last match.
=> "am"
irb(main):019:0> matches[0]          # whole match
=> "15:45"
irb(main):020:0> matches[1]          # first sub match
=> "15"
irb(main):021:0> matches[2]          # second sub match
=> "45"
irb(main):022:0> matches[3]          # nothing after that. 
=> nil
irb(main):023:0>
</pre>

Note, the pre_match and post_match will return the substrings before and after the whole matches[0].
 
There are also a bunch of <strong>global variables</strong> that gets set/updated every time you use the "=~" or ".match" method, they are:

<ul>
	<li>$` - this is the equivalent to the pre_match method</li>
	<li>$' - this is equivalent to the pust_match method</li>
	<li>$1, $2....etc   - these are the sub-matches resulting from the (...)</li>


</ul>

note, there is no $0 variable. 

Here they are in action (after starting up a new irb session):


<pre>



PS C:\Temp\irb> irb
irb(main):001:0> $`       # to begin with the don't hold values. 
=> nil                
irb(main):002:0> $'       
=> nil
irb(main):003:0> $1
=> nil
irb(main):004:0> "hello world" =~ /elx/ 
=> nil
irb(main):005:0> $`           # Still doesn't hold values since no match made
=> nil
irb(main):006:0> $'
=> nil
irb(main):007:0> $1
=> nil
irb(main):008:0> "hello world" =~ /el/
=> 1
irb(main):009:0> $`
=> "h"
irb(main):010:0> $'
=> "lo world"
irb(main):011:0> $1          # Still doesn't hold value, since ther were no sub-expressions, .i.e. (...)
=> nil
irb(main):012:0> matches = /(\d+):(\d+)/.match("The current time is 15:45am")
=> #<MatchData "15:45" 1:"15" 2:"45">
irb(main):013:0> $`
=> "The current time is "
irb(main):014:0> $'
=> "am"
irb(main):015:0> $1
=> "15"
irb(main):016:0> $2
=> "45"
irb(main):017:0>

</pre>

If you want to match for all patterns and capture them, rather than just capturing just the first match, then you can use the "scan" method instead of the match method, i.e. :

<pre>
irb(main):040:0* all_matches = "The current time is 15:45am".scan(/\d+/)
=> ["15", "45"]
irb(main):041:0> all_matches[0]
=> "15"
irb(main):042:0> all_matches[1]
=> "45"
</pre>

As you can see, the way the scan method is written is slightly different to the match method. 

Also there is no pre_match or post_match, e.g. like pre_scan and post_scan don't exist. 

However, the global variables still work:

<pre>
irb(main):047:0> all_matches = "The current time is 15:45am".scan(/\d+/)
=> ["15", "45"]
irb(main):048:0> all_matches[0]
=> "15"
irb(main):049:0> all_matches[1]
=> "45"
irb(main):050:0> $`
=> "The current time is 15:"  # Here the 15: means the we captured this during the last "scan-cycle"
irb(main):051:0> $'
=> "am"
</pre>



Next we have the "gsub" string, this is a bit of a combination between regex and linux's awk system, here's an example:

<pre>irb(main):068:0> message = "hello world"
=> "hello world"
irb(main):069:0> reverse_message = message.gsub(/([A-Za-z]+) ([A-Za-z]+)/, "\\2 \\1")
=> "world hello"
irb(main):070:0> puts reverse_message
world hello
=> nil
irb(main):071:0></pre>

Note the "\\2 \\1" could have been rewritten with single quotes '\2 \1', e.g. :

<pre>
irb(main):072:0> reverse_message = message.gsub(/([A-Za-z]+) ([A-Za-z]+)/, '\2 wide \1')
=> "world wide hello"
</pre>

gsub also lets you do some more complex stuff by allowing you to pass the matches into a code block:

<pre>
irb(main):073:0> uncrypted = "Password: LiverPool"
=> "Password: LiverP0ol"
irb(main):074:0> encrypted = uncrypted.gsub(/(Password:)\s+(\w+)/) { |matches| "#{$1} " + "?"*$2.length }
=> "Password: ?????????"
irb(main):075:0>
</pre>

Here's a quick reference to the escaped characters:

\d - a digit
\D - anything except a digit
\h - an character  (0-9a-zA-Z)
\H - anything except a character
\R - A generic linebreak sequence, i.e. \r\n
\s - Any whitespace character, e.i. space or tab
\S - Anaything except a whitespace character
\w - A word, i.e. shorthand for [a-zA-Z1-9]+
\W - anything except a word



You can match the start and end of a line like this:

<pre>


irb(main):105:0> matches = /^el/.match("hello world")
=> nil
irb(main):106:0> matches = /^hel/.match("hello world")
=> #<MatchData "hel">
irb(main):107:0> matches[0]
=> "hel"
irb(main):111:0> matches = /worl$/.match("hello world")
=> nil
irb(main):112:0> matches = /world$/.match("hello world")
=> #<MatchData "world">
irb(main):113:0> matches[0]
=> "world"
irb(main):114:0>

</pre>


You can ignore upper/lower case using the "i" option, by placing it straight after the regex expression:



<pre>irb(main):121:0> matches = /hel/i.match("hEllo world")
=> #<MatchData "hEl">
</pre>