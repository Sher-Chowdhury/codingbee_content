---
ID: 147
post_title: 'WIX &#8211; Create virtual directory'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/wix/wix-create-virtual-directory
published: true
post_date: 2014-08-28 00:00:00
---
<ol>
	<li>first you include the &lt;Wix xmlns="http://schemas.microsoft.com/wix/2006/wi"<strong> xmlns:iis="http://schemas.microsoft.com/wix/IIsExtension"</strong>&gt; at the start of the xml file</li>
	<li>The virtualdirectory needs to point to a real life folder (aka physical path). To do this you first add compoenent inside the directory-element that represents the physical path.</li>
	<li>Insite this you add 2 elements. &lt;createFolder /&gt; and &lt;iis:WebVirtualDir Id="VDir" WebSite="DefaultWebSite" Alias="{this is the folder's name as it appears in inetmgr}"   Directory="{name of physical path's folder}"&gt;</li>
	<li>Just before feature's element, enter:&lt;iis:WebSite Id='DefaultWebSite'
Description='Default Web Site'
Directory='{name of physical path's folder}'&gt;
&lt;!-- This element has to be here or WiX does not compile. It's ignored in this case. --&gt;
&lt;iis:WebAddress Id="AllUnassigned" Port="80" /&gt;
&lt;/iis:WebSite&gt;</li>
	<li>On the command line, cd to the folder contain the wxs file, then use the iis extension:
&amp; 'C:\Program Files (x86)\WiX Toolset v3.8\bin\light.exe' .\product.wixobj -ext 'C:\Program Files (x86)\WiX Toolset v3.8\bin\WixIIsExtension.dll'</li>
</ol>
&nbsp;

&nbsp;

http://www.wintellect.com/blogs/jrobbins/install-a-new-virtual-directory-to-default-web-site-with-wix