---
ID: 149
post_title: 'WIX &#8211; Embed powershell into an MSI file'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/wix/wix-embed-powershell-into-an-msi-file
published: true
post_date: 2014-08-29 00:00:00
---
You can embed a single powershell command into an wxs file like this:

&nbsp;

<pre>
<wix ....>
    <product .....>
        <CustomAction
	    Id="EXE_RunPowershell" 
	    Directory="DIR_v1.0"
	    ExeCommand="[DIR_v1.0]powershell.exe &quot;ls | out-file c:\temp\\powerdummy.txt&quot; "     
	    Execute="deferred" 
	    Return="check" />

            <InstallExecuteSequence>
	        <Custom Action="EXE_RunPowershell" After="InstallFiles">
		    NOT Installed AND NOT PATCH
		</Custom>
	    </InstallExecuteSequence>
    <product>
</wix>
</pre>

&nbsp;
Note, if DIR_v1.0 path contains spaces, e.g. "program files", then you need to use "&qoute;"
&nbsp;

Here's another example, but this time using installutil.exe as an example:

<pre>
<CustomAction
			Id="EXE_InstallUtil" 
			Directory="DIR_v2"
			ExeCommand="[DIR_v2]InstallUtil.exe &quot;[DIR_QueueManagementService]dummy.exe&quot;"
			Execute="deferred" 
			Return="check"/>

		<InstallExecuteSequence>
			<Custom Action="EXE_InstallUtil" After="InstallFiles">
				NOT Installed AND NOT PATCH
			</Custom>
		</InstallExecuteSequence>


		<CustomAction
			Id="EXE_U_InstallUtil" 
			Directory="DIR_v2"
			ExeCommand="[DIR_v2]InstallUtil.exe /u &quot;[DIR_QueueManagementService]dummy.exe&quot;"
			Execute="deferred" 
			Return="check"/>

		<InstallExecuteSequence>
			<Custom Action="EXE_U_InstallUtil" After="RemoveFiles">
				Installed
			</Custom>
		</InstallExecuteSequence>
</pre>

Any folder reference's e.g. "DIR_v2" within square-brackets automatically get's expanded. 

https://www.google.co.uk/search?hl=en-GB&amp;ie=UTF-8&amp;source=android-browser&amp;q=embed+powershell+wix&amp;gfe_rd=cr&amp;ei=JHj_U7_nNPHH8geBxoHYBQ&amp;gws_rd=ssl#hl=en-GB&amp;q=embed+powershell+wix


http://stackoverflow.com/questions/2313545/execute-command-line-in-wix-script 

http://stackoverflow.com/questions/15472793/custom-action-on-install-only