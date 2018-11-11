---
ID: 143
post_title: 'WIX &#8211; What is Windows Installer XML'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/wix/wix-what-is-windows-installer-xml
published: true
post_date: 2014-08-25 00:00:00
---
The Windows Installer XML is   a powerful utility for packaging a softare into an MSI file.

&nbsp;

If you have a software that you want make to available to others, so that they can install it on their own Windows Machine, then Microsoft recommends that you first package up your software into a single MSI file before distributing it.

Windows Installer XML (WIX) is a free open-source tool that lets you package your software into an MSI file. To create an MSI using wix, all you essentially need to do is create an xml file then feed that xml file into WIX along with all your software's source/binary files. WIX will then handle the rest and generate the MSI file for you.

Once the MSI file has been created, you can then install the software by:
<ol>
	<li>Double clicking on the msi file -     an install wizard pop-up will appear prompting the user for more info</li>
	<li>Run msi file from the command line - this method is often used if you want to install the software silently. With this option you can provide all the info the windows installer needs in a single command and thereby suppressing any install wizard windows popping up.   Hence this approach is really useful if you want to automate software installations.</li>
</ol>
WIX is used from the command line only. However WIX is also integrated in a lot of big name IDE's such as Visual Studio.

Here are some of benefits of packaging your software into an MSI file:
<ul>
	<li>All your software files are can be packaged into one convenient .msi file.</li>
	<li>Your software will automatically be registered with Progam and Features</li>
	<li>Windows can easily uninstall your software should the user select the uninstall option in Program and Features</li>
	<li>If any of your software's file are accidently removed, then this can be fixed by right-clicking on the MSI file and select repair.</li>
	<li>You can create msi file for different versions of your software and the msi package can detect which version has been installed.</li>
	<li>you can create patches to update only specific areas of your application.</li>
	<li>If something goes wrong during the isntall process, then the windows installer can easily roll back to a previous state.</li>
	<li>You can create Install Wizard pop windows to guide user's through the install process.</li>
</ul>
All of these benefits comes included as part of offering your software in the form of an MSI package. There are lots of <a href="http://en.wikipedia.org/wiki/Windows_Installer#Tools">MSI creation tools</a> out there, but WIX has emerged as one of the market leaders in this space.

&nbsp;

Before you can use WIX, you first have to <a href="http://wixtoolset.org/">install wix</a>.

&nbsp;

After the install, the wix install folder should contain the following exe-based commandline utilities:

[powershell]
PS C:\Program Files (x86)\WiX Toolset v3.8\bin&amp;gt; ls | where-Object -f {$_.name -match &amp;quot;.exe
$&amp;quot;}

    Directory: C:\Program Files (x86)\WiX Toolset v3.8\bin

Mode                LastWriteTime     Length Name
----                -------------     ------ ----
-a---        28/11/2013     05:13      28672 candle.exe
-a---        28/11/2013     05:14      28672 dark.exe
-a---        28/11/2013     05:14      28672 heat.exe
-a---        28/11/2013     05:14      24576 insignia.exe
-a---        28/11/2013     05:13      32768 light.exe
-a---        28/11/2013     05:14      28672 lit.exe
-a---        28/11/2013     05:16      32768 lux.exe
-a---        28/11/2013     05:14      28672 melt.exe
-a---        28/11/2013     05:16      28672 nit.exe
-a---        28/11/2013     05:14      32768 pyro.exe
-a---        28/11/2013     05:14      49152 shine.exe
-a---        28/11/2013     05:14      28672 smoke.exe
-a---        28/11/2013     05:14     109056 ThmViewer.exe
-a---        28/11/2013     05:14      32768 torch.exe
-a---        28/11/2013     05:14      86016 WixCop.exe
[/powershell]

You can read up about these <a href="http://wixtoolset.org/documentation/manual/v3/overview/">wix tools</a>.

When creating an xml, you need to provide guid values. An easy way to do this is just use <a href="http://wixtoolset.org/documentation/manual/v3/howtos/general/generate_guids.html">the online guid generation tool</a>. This page also says wix can autogenerate this using *, this might be the better approach in fast moving continuous integration. Although when you do official release then you should generate the guid using the online tool and then hard code them in.

Here's what a typical main wix source xml file looks like (note this file has the extensiont ".wxs")

[xml]
&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot;?&gt;
&lt;Wix xmlns=&quot;http://schemas.microsoft.com/wix/2006/wi&quot;&gt;
    &lt;Product Id=&quot;3E786878-358D-43AD-82D1-1435ADF9F6EA&quot; Name=&quot;Awesome Software&quot; Language=&quot;1033&quot; Version=&quot;1.0.0.0&quot; Manufacturer=&quot;Awesome Company&quot; UpgradeCode=&quot;B414C827-8D81-4B4A-B3B6-338C06DE3A11&quot;&gt;
        &lt;Package InstallerVersion=&quot;301&quot; Compressed=&quot;yes&quot; InstallScope=&quot;perMachine&quot; Manufacturer=&quot;Awesome Company&quot; Description=&quot;Installs Awesome Software&quot; Keywords=&quot;Practice,Installer,MSI&quot; Comments=&quot;(c) 2012 Awesome Company&quot; /&gt;
        &lt;MediaTemplate EmbedCab=&quot;yes&quot; /&gt;
        &lt;!--Directory structure--&gt;

        &lt;Directory Id=&quot;TARGETDIR&quot; Name=&quot;SourceDir&quot;&gt;    &lt;!-- This if hardcoded in: http://stackoverflow.com/questions/1641094/in-wix-files-what-does-name-sourcedir-refer-to --&gt;
            &lt;Directory Id=&quot;ProgramFilesFolder&quot;&gt;    &lt;!--&quot;ProgramsFilesFolder&quot; is a built-in property: http://msdn.microsoft.com/en-us/library/aa370905%28v=vs.85%29.aspx#system_folder_properties -- also you can specify whether it is 64bit or x86, from the command line using the candle.exe's &quot;arch&quot; switch --&gt;
                &lt;Directory Id=&quot;MyProgramDir&quot; Name=&quot;Awesome Software&quot; /&gt; &lt;!-this &quot;directory&quot; element will create a new folder called &quot;Awesome Software&quot;, see: http://wixtoolset.org/documentation/manual/v3/xsd/wix/directory.html---&gt;
                &lt;Directory Id=&quot;ProgramMenuFolder&quot;&gt;
                    &lt;Directory Id=&quot;MyShortcutsDir&quot; Name=&quot;Awesome Software&quot; /&gt;
                &lt;/Directory&gt;
            &lt;/Directory&gt;
        &lt;/Directory&gt;
        &lt;!--Components--&gt;
        &lt;DirectoryRef Id=&quot;MyProgramDir&quot;&gt;
            &lt;Component Id=&quot;CMP_InstallMeTXT&quot; Guid=&quot;E8A58B7B-F031-4548-9BDD-7A6796C8460D&quot;&gt;
                &lt;File Id=&quot;FILE_InstallMeTXT&quot; Source=&quot;InstallMe.txt&quot; KeyPath=&quot;yes&quot; /&gt;
            &lt;/Component&gt;
        &lt;/DirectoryRef&gt;
        &lt;!--Start Menu Shortcuts--&gt;
        &lt;DirectoryRef Id=&quot;MyShortcutsDir&quot;&gt;
            &lt;Component Id=&quot;CMP_DocumentationShortcut&quot; Guid=&quot;33741C82-30BF-41AF-8246-44A5DCFCF953&quot;&gt;
                &lt;Shortcut Id=&quot;DocumentationStartMenuShortcut&quot; Name=&quot;Awesome Software Documentation&quot; Description=&quot;Read Awesome Software Documentation&quot; Target=&quot;[MyProgramDir]InstallMe.txt&quot; /&gt;
                &lt;Shortcut Id=&quot;UninstallShortcut&quot; Name=&quot;Uninstall Awesome Software&quot; Description=&quot;Uninstalls Awesome Software&quot; Target=&quot;[System64Folder]msiexec.exe&quot; Arguments=&quot;/x [ProductCode]&quot; /&gt;
                &lt;RemoveFolder Id=&quot;RemoveMyShortcutsDir&quot; On=&quot;uninstall&quot; /&gt;
                &lt;RegistryValue Root=&quot;HKCU&quot; Key=&quot;Software\Microsoft\AwesomeSoftware&quot; Name=&quot;installed&quot; Type=&quot;integer&quot; Value=&quot;1&quot; KeyPath=&quot;yes&quot; /&gt;
            &lt;/Component&gt;
        &lt;/DirectoryRef&gt;
        &lt;!--Features--&gt;
        &lt;Feature Id=&quot;ProductFeature&quot; Title=&quot;Main Product&quot; Level=&quot;1&quot;&gt; &lt;!-- Note all components must have a corresponding feature --&gt;
            &lt;ComponentRef Id=&quot;CMP_InstallMeTXT&quot; /&gt;
            &lt;ComponentRef Id=&quot;CMP_DocumentationShortcut&quot; /&gt;
        &lt;/Feature&gt;
    &lt;/Product&gt;
&lt;/Wix&gt;
[/xml]

All these elements, e.g. "directory element" are described in the manual. Just do word find (ctrl+f) on this page and search for "wix schema". the childs of this node covers all this.

Also the <a href="http://wixtoolset.org/documentation/manual/v3/">official wix documentation</a> has lots of hand info.

Once you have created wxs file, place this in the same folder as your source code. Open up powershell and cd to the location of your wxs file. Then run the following command:

[powershell]

PS C:\Users\SChowdhury\Desktop\DummyApp&gt; ls

    Directory: C:\Users\SChowdhury\Desktop\DummyApp

Mode                LastWriteTime     Length Name
----                -------------     ------ ----
-a---        26/08/2014     08:56         26 DummyDLLFile.txt
-a---        26/08/2014     09:15       1155 Product.wxs

PS C:\Users\SChowdhury\Desktop\DummyApp&gt; &amp; 'C:\Program Files (x86)\WiX Toolset v3.8\bin\candle.exe' .\Product.wxs
Windows Installer XML Toolset Compiler version 3.8.1128.0
Copyright (c) Outercurve Foundation. All rights reserved.

Product.wxs
PS C:\Users\SChowdhury\Desktop\DummyApp&gt; ls

    Directory: C:\Users\SChowdhury\Desktop\DummyApp

Mode                LastWriteTime     Length Name
----                -------------     ------ ----
-a---        26/08/2014     08:56         26 DummyDLLFile.txt
-a---        26/08/2014     12:01       6205 Product.wixobj      # new file created
-a---        26/08/2014     09:15       1155 Product.wxs

PS C:\Users\SChowdhury\Desktop\DummyApp&gt;
[/powershell]

The candle.exe will even create the wixobj file even if it encounters minor errors. If you want candle.exe to stop as soon as it encounters any small error's then use the "pedantic" option:

[powershell]
PS C:\Users\SChowdhury\Desktop\DummyApp&gt; &amp; 'C:\Program Files (x86)\WiX Toolset v3.8\bin\candle.exe' .\Product.wxs -pedantic
Windows Installer XML Toolset Compiler version 3.8.1128.0
Copyright (c) Outercurve Foundation. All rights reserved.

Product.wxs
C:\Users\SChowdhury\Desktop\DummyApp\Product.wxs(3) : error CNDL0087 : The Product/@Id attribute's value, '774cdf65-1c3a-4650-8868-b4c090e74c5a', is a mixed-case guid.  All letters in a guid value should be uppercase.
C:\Users\SChowdhury\Desktop\DummyApp\Product.wxs(3) : error CNDL0087 : The Product/@UpgradeCode attribute's value, '478ebf08-ca88-4e3b-9460-2db6f6584e6e', is a mixed-case guid.  All letters in a guid value should be uppercase.
PS C:\Users\SChowdhury\Desktop\DummyApp&gt;
[/powershell]

This time no files have been generated.
http://msdn.microsoft.com/en-us/magazine/cc163456.aspx

http://en.wikipedia.org/wiki/List_of_build_automation_software
&nbsp;

&nbsp;

http://wixtoolset.org/

&nbsp;

http://wix.tramontana.co.hu/tutorial

&nbsp;

http://en.wikipedia.org/wiki/Windows_Installer

&nbsp;

Puppet's "<a href="https://docs.puppetlabs.com/references/latest/type.html#package">package</a>" resource type also handle msi natively.

Windows Installer documentation: http://msdn.microsoft.com/en-us/library/cc185688

Alternatives to WIX - http://en.wikipedia.org/wiki/List_of_installation_software