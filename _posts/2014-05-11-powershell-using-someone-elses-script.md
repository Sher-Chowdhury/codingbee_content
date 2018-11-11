---
ID: 77
post_title: 'PowerShell &#8211; Using Someone else&#8217;s script'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-using-someone-elses-script
published: true
post_date: 2014-05-11 00:00:00
---
Chapter 26 - Using Someone else's script

for lots of sample powershell scripts, check out this code repository:

www.poshcode.org

Here is the structure of the if-else statement that you can place inside a script:

[cmdletbinding()]
	param(
		[parameter(mandatory=$true)]
		[int] $var
	)

# Here is an if-elseif-else statement. For some reason this is if-conditions are if-else conditions are not really covered in this book
# but is covered in the 2nd book. 
if ($var -gt 2)
{
    Write-Host "The value $var is greater than 2."
}
elseif ($var -eq 2)
{
    Write-Host "The value $var is equal to 2."
}
else
{
    Write-Host "The value $var is less than 2 or was not created or initialized."
}
	

In bash, when you want to check whether an ftp/scp transfer has been successful, then you check the exit code ($?) right after 
running the ftp/scp command. This check is done by placing the exit code within it's own if statement, i.e.:

if [ $? -gt 0 ]; then 
	log "file transfer failed"
	archive fail $file
fi


However, in ps, you do the same thing, but with construct called the try-catch. Covered in the next book.