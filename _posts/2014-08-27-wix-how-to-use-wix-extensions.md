---
ID: 146
post_title: 'WIX &#8211; How to use WIX Extensions'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/wix/wix-how-to-use-wix-extensions
published: true
post_date: 2014-08-27 00:00:00
---
In the wxs file, you have bunch of elements out of the box, e.g. component, directory,....etc. However there are other elements which you can use, by loading in some extensions. You have to load the extensions in when running the light.exe command, using the -ext option. A bunch of extensions comes included with wix but not loaded in. You can find them here:

[powershell]

PS C:\Program Files (x86)\WiX Toolset v3.8\bin&gt; ls | Where-Object -f {$_.name -match &quot;Extension.dll$&quot;}


    Directory: C:\Program Files (x86)\WiX Toolset v3.8\bin


Mode                LastWriteTime     Length Name
----                -------------     ------ ----
-a---        28/11/2013     05:14     266240 WixBalExtension.dll
-a---        28/11/2013     05:15     466944 WixComPlusExtension.dll
-a---        28/11/2013     05:15      98304 WixDependencyExtension.dll
-a---        28/11/2013     05:15      28672 WixDifxAppExtension.dll
-a---        28/11/2013     05:15      61440 WixDirectXExtension.dll
-a---        28/11/2013     05:15      98304 WixFirewallExtension.dll
-a---        28/11/2013     05:16      90112 WixGamingExtension.dll
-a---        28/11/2013     05:16     405504 WixIIsExtension.dll
-a---        28/11/2013     05:16     163840 WixLuxExtension.dll
-a---        28/11/2013     05:16     139264 WixMsmqExtension.dll
-a---        28/11/2013     05:16     290816 WixNetFxExtension.dll
-a---        28/11/2013     05:16      36864 WixPSExtension.dll
-a---        28/11/2013     05:16     270336 WixSqlExtension.dll
-a---        28/11/2013     05:16      49152 WixTagExtension.dll
-a---        28/11/2013     05:16    3637248 WixUIExtension.dll
-a---        28/11/2013     05:16     729088 WixUtilExtension.dll
-a---        28/11/2013     05:16     937984 WixVSExtension.dll

[/powershell]


However there are others that are availabe but in the form of an extension that needs to be loaded in.

&nbsp;

http://wixtoolset.org/documentation/manual/v3/howtos/general/extension_usage_introduction.html

http://wixtoolset.org/documentation/manual/v3/xsd/iis/webvirtualdir.html

&nbsp;

https://www.google.co.uk/search?q=wix+iis&amp;ie=utf-8&amp;oe=utf-8&amp;aq=t&amp;rls=org.mozilla:en-GB:official&amp;client=firefox-a&amp;gfe_rd=cr&amp;ei=g7D8U93lGpeCbK70gJgB#q=wix+virtual+directory&amp;rls=org.mozilla:en-GB:official&amp;safe=off

&nbsp;

http://wixtoolset.org/documentation/manual/v3/wixui/wixui_dialog_library.html

&nbsp;

http://wixtoolset.org/documentation/manual/v3/xsd/iis/webvirtualdir.html

&nbsp;

&nbsp;