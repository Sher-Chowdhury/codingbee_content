---
ID: 98
post_title: 'PowerShell &#8211;  Run sql queries (using sqlplus)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-run-sql-queries-using-sqlplus
published: true
post_date: 2014-05-11 00:00:00
---
<h2>What is SQLPLUS?</h2>

If you have an Oracle database and you want to run sql queries/scripts against it, then you can use "sqlplus", which is Oracle's own command line utility for running sql scripts including those in in Oracle's powerful <a href="http://amzn.to/1PmFTq3">Oracle PL/SQL Programming PL/SQL scripting language</a>. You can install sqlplus onto your machine by installing the <a href="http://www.oracle.com/technetwork/topics/winsoft-085727.html">oracle client</a>. Sqlplus also comes included with <a href="http://www.oracle.com/technetwork/developer-tools/sql-developer/downloads/index.html">Oracle SQL Developer</a>.

Once you have sqlplus installed, you then need to add a valid tns entry to the <a href="http://www.orafaq.com/wiki/Tnsnames.ora">tns file</a>. 

sqlplus was originally designed to be called using the <a href="http://en.wikipedia.org/wiki/Cmd.exe">old legacy cmd</a>. However it works just as well using Powershell (although  sqlplus won't run properly if run from inside the Powershell ISE).

Powershell users can use sqlplus in three main ways:
<ul>
	<li>Start an interactive sqlplus session from the powershell command line</li>
	<li>Run a sql script from inside a powershell script</li>
	<li>Embed sql code directly into powershell scripts</li>
</ul>
&nbsp;
<h2>Start an interactive sqlplus session from the powershell command line</h2>
To do this, you simply open up a powershell terminal and then do:

<pre>
sqlplus username/password@TnsAlias
</pre>

This puts you into an interactive sqlplus session where you can start running sql statements. You can exit the session by typing "exit".

&nbsp;

This is done in the same way in CMD as well.

You can also run sql scripts as well, but for this to work you first need to navigate to the directory that contains the sql scripts. Once your powershell session is in that directory, you then enter the sqlplus session. After that you can run the script like this:

<pre>
SQL> @DBscriptName.sql;
</pre>
This approach is great if you want to run a db script/query manually but if you want to automate this then you need to look at the next two approaches:

<h2>Run a SQL script from inside a powershell script</h2>
<pre>
sqlplus username/password@TnsAlias 'c:\path\to\DBscript.sql' | out-file 'c:\temp\sql-output.txt'
</pre>

Here we chose to capture the sqlplus's output into a file, sql-output.txt. However although we could have also captured it into a powershell variable as well, like this:

<pre>
$output = sqlplus username/password@TnsAlias 'c:\path\to\DBscript.sql'
</pre>

This output might not quite look like the way you want it. Luckily there are a bunch of <a href="http://docs.oracle.com/cd/B19306_01/server.102/b14357/ch6.htm">sqlplus formatting</a> statements that you can insert at the top of your DBscript.sql to fix this.

If you want the output to be read/manipulated by Powershell (either manually or via a powershell script), then you may want to remove as much sqlplus formatting as possible, in which case here are a couple of useful formatting options that could be worth applying:

"set heading off" - this <a href="http://docs.oracle.com/cd/B19306_01/server.102/b14357/ch12040.htm#i2699001">switches-off column titles</a>.
"set newpage none" - This <a href="http://docs.oracle.com/cd/B19306_01/server.102/b14357/ch12040.htm#i2678637">removes any whitespacing</a> at the begining of the output.
"SET FEEDBACK OFF" - this <a href="http://docs.oracle.com/cd/B19306_01/server.102/b14357/ch12040.htm#i2698970">suppresses displaying the a count of the number of rows selected</a>, i.e. it wont show "xxx rows selected"
"SET PAGESIZE o" - I have used this so to further neutralize sqlplus formatting.

&nbsp;

Also note, the captured content will also contain the usual "you have successfully connected to Oracle db" message, which gets outputted as soon as a new sqlplus session is created. If you want to suppress this message, then simply use the "-silent" command. e.g.

<pre>
sqlplus username/password@TnsAlias '@c:\path\to\DBscript.sql' -silent | out-file 'c:\temp\sql-output.txt' </pre>

<h3>Running a sql script from the powershell command line</h3>
Let's say we have a simple sql script called, c:\temp\simplequery.sql, and it's content is:

<pre>
set NewPage none
set heading off
SELECT username FROM dba_users;
exit
</pre>

This is a simple sql script that should output a list of database user accounts (i.e. schemas in an oracle db). After that, you can then simply call this sql script like this:

<pre>
PS C:\> $username = "codingbee"
PS C:\> $password = "liverpool"
PS C:\> $tnsalias = "xyz"
PS C:\> $script = "c:\temp\simplequery.sql"
PS C:\> $outputfile = "C:\temp\activeusers.txt"
PS C:\> sqlplus -silent $username/$password@$tnsalias "$script" | Out-File $outputfile
</pre>

As you can see, it is quite straightforward to write the above commands into a powershell script too, which will require a sql script as an input for the powershell script to run.  

<h3>Run sql queries from inside a powershell script</h3>

However, a really cool thing you can also do, is embed sql queries directly into a powershell script. This can be useful, if you want to run a really simple sql query, in which case having it in it's own sql file would be overkill. 

You do this by piping the sql query in, with the help of the "here-strings". A here-string is basically a technique for creating a (multiline) string variable. Here is the syntax you use to create it:


<pre>PS C:\> $MultilineVar = @"
>> line number one
>> line number two
>> "@
>>
PS C:\> $MultilineVar
line number one
line number two
PS C:\>
</pre>


In our case, all we do is somewhere inside our powershell script, we use the here-string to create a multi-line string variable containing the sql code, Then pipe this multiline variable into the sqlplus command, and finally capture the output, either by piping it to "out-file" or in a variable, e.g.:

<pre>	$sqlQuery = @"
		set NewPage none
		set heading off
		set feedback off
		SELECT username FROM dba_users;
		exit
	"@

	$username = "CodingBee"
	$password = "Liverpool"
	$tnsalias = "simple.world"
	$outputfile = "C:\Temp\activeusers.txt"      # this is only required for option 1

        # You can now do one of 2 things.

        # Option 1 - piping the sql output into a file. 
	$sqlQuery | sqlplus -silent $username/$password@$tnsalias | Out-File $outputfile

        # Option 2 - capture the sql output into a variable. 
	$sqlOutput = $sqlQuery | sqlplus -silent $username/$password@$tnsalias 


</pre>
You can put the above in a ps1 file and it will run fine.

<strong>
See also</strong>


<a href="http://docs.oracle.com/cd/E11882_01/server.112/e16604/toc.htm">
sqlplus user's guide</a>



[post-content id=5393]