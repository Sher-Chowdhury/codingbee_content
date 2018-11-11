---
ID: 153
post_title: 'WIX &#8211; edit xml file with the help of xpath notation'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/wix/wix-edit-xml-file-with-the-help-of-xpath-notation
published: true
post_date: 2014-09-02 00:00:00
---
We can apply changes to an xml file, we can use the xmlfile element which is part of the Util Extension:

http://wixtoolset.org/documentation/manual/v3/xsd/util/xmlfile.html


The following copies the file across and makes a change to an element's value:

<DirectoryRef Id="DIR_folder">
    <Component Id="CMP_Web.config" Guid="*">
        <File Id="FILE_Web.config" Source="FGWCF.Host\Web.config" KeyPath="yes" />
	    <util:XmlFile 
	        Id='XmlSettings1' 
		File='[DIR_folder]Web.config'    
		Action='setValue'
		Value='woooohooooooooooooooooooooooooooooooooooooooooo'
		ElementPath="//path/to/node[\[]@name='connection.connection_string'[\]]" 
		Sequence='1' />	
    </Component>
</DirectoryRef>  

Note: It singles out en element that has an attribute called "name", and has value "connection.connection_string". 

Note: The "[\[]" is an escaped opening square bracket, also "[\]]" is an escaped closing square bracket. That's because in wix, square brackets have a special meaning, i.e. it indicates a variable. However we are using the square brackets as part of xpath notation, that's why they needed to be escaped. 

http://wix.tramontana.co.hu/tutorial/com-expression-syntax-miscellanea/xml

http://wixtoolset.org/documentation/manual/v3/xsd/util/xmlfile.html