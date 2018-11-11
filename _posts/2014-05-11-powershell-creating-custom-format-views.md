---
ID: 86
post_title: 'PowerShell &#8211; Creating Custom Format Views'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-creating-custom-format-views
published: true
post_date: 2014-05-11 00:00:00
---
Chapter 12 - Creating Custom Format Views

In chapter 7 you learn about how to create a custom (collection) object. However PS may not display the object in your preferred format (and order) that you want, since there are no xml-formatting file that tells ps how to format your custom object. Therefore some of your columns may be wider/narrower than you would have preferred, or the ordering of the commands could be wrong. 

A way round this issue is to create your own formatting for your given custom object. The best way to do this is by using an existing xml formatting file as a template:

PS C:\> cd $PSHOME
PS C:\WINDOWS\system32\WindowsPowerShell\v1.0> Get-ChildItem | Where-Object -FilterScript {$_.name -match ".xml$"}

Note: your custom view xml file will need to have the ".ps1xml" extension, which indicates that it is a "Windows Powershell XML Document".

e.g. lets say you have the following function:

function new-hmrcobject {

	$MyHashtable = @{'column1'="aaa";
					 'column2'="bbb";
					 'column3'="ccc"
	}

	$obj = New-Object -TypeName psobject -property $MyHashtable
	$obj.PSObject.TypeNames.Insert(0,'hrmctoolcustomformat')		# Here we attach our object to a custom view called "hrmctoolcustomformat"
	
	$obj
}

Now I create a custom view using the following file as a template:

C:\WINDOWS\system32\WindowsPowerShell\v1.0\DotNetTypes.format.ps1xml

Note: here is a list of custom views that already comes with ps:

Where-Object -FilterScript {$_.name -match ".format.ps1xml"}  	

Mode                LastWriteTime     Length Name
----                -------------     ------ ----
-a---        10/06/2009     21:41      27338 Certificate.format.ps1xml
-a---        10/06/2009     21:41      27106 Diagnostics.Format.ps1xml
-a---        23/07/2012     19:12     144442 DotNetTypes.format.ps1xml
-a---        23/07/2012     19:12      14502 Event.Format.ps1xml
-a---        23/07/2012     19:12      21293 FileSystem.format.ps1xml
-a---        23/07/2012     19:12     287938 Help.format.ps1xml
-a---        23/07/2012     19:12      97880 HelpV3.format.ps1xml
-a---        23/07/2012     19:12     101824 PowerShellCore.format.ps1xml
-a---        10/06/2009     21:41      18612 PowerShellTrace.format.ps1xml
-a---        23/07/2012     19:12      13659 Registry.format.ps1xml
-a---        23/07/2012     19:12      17731 WSMan.Format.ps1xml

Note, a custom view must always end with ".format.ps1xml" 

Will use DotNetTypes.format.ps1xml as a template. Using this file I created a file called "hrmctools.formatps1xml", and it contains the following:

<?xml version="1.0" encoding="utf-8" ?>
<Configuration>
    <ViewDefinitions>     
		<View>
            <Name>hrmctoolcustomformat</Name>		# This must match with the "insert" command above so that object is linked to this format 
            <ViewSelectedBy>
                <TypeName>hrmctoolcustomformat</TypeName> 	# This must match with the "insert" command above so that object is linked to this format
            </ViewSelectedBy>

            <TableControl>
                <TableHeaders>
                    <TableColumnHeader>
                        <Label>columnX</Label>		# This displays a different name rather than defaulting to the property name. 
                        <Width>40</Width>			# This defines the column width. 
                    </TableColumnHeader>
					<TableColumnHeader>
                        <Label>columnY</Label>
                        <Width>15</Width>
                    </TableColumnHeader>
                    <TableColumnHeader>
						<Label>columnZ</Label>
                        <Width>15</Width>
                    </TableColumnHeader>
                </TableHeaders>
                <TableRowEntries>
                    <TableRowEntry>
                        <TableColumnItems>
                            <TableColumnItem>
                                <PropertyName>column1</PropertyName>	# Here you can re-arrange the column ordering. 
                            </TableColumnItem>
                            <TableColumnItem>
                                <PropertyName>column2</PropertyName>
                            </TableColumnItem>
							<TableColumnItem>
                                <PropertyName>column3</PropertyName>
                            </TableColumnItem>
                        </TableColumnItems>
                    </TableRowEntry>
                 </TableRowEntries>
            </TableControl>
        </View>
    </ViewDefinitions>
</Configuration>

Note: In PS, the content of xml files are always CASE SENSITIVE!!! 


Once you have created your own custom view you then need to tell ps to activate it by using (Update-FormatData):

Update-FormatData -PrependPath c:\path\to\xml\file

Once you run the above command, this format should now be loaded into memory. You can check this using "get-formatdata":

get-formatdata



You can then attach it your custom view like this using the "insert" method:

$MyObject.PSObject.TypeNames.Insert(0,'hrmctoolcustomformat')	# Note, this has already been done in the above function.

The first parameter "0" is something that you always type in. You can now confirm that the object has successfully been attached to the custom view, by doing:

$MyObject | gm

Also if you output your object, you should now notice that its appearance should have now changed to those that you defined in the custom view. 

A "Typename" is essentially a name that you give to your object. It tell's PS the type of object that it is. It is possible for a number of commands 
outputting objects of the same type (i.e. they can have the same typename value).

Note: if you lookup gm for $myobject, you will find that "PSObject" is not listed as a property, however it is listed if you use "-force":

$MyObject | Get-Member -Force