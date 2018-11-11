---
ID: 120
post_title: 'Python &#8211; run python scripts'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/python/python-run-python-scripts
published: true
post_date: 2014-06-15 00:00:00
---
You have to do this from the powershell command line:

&nbsp;
<ol>
	<li>user powershell to cd to the directory containin the py file.</li>
	<li>from the powershell command line type "python {script-name}.py"</li>
</ol>
&nbsp;

to load From within python, check out:

&nbsp;

http://stackoverflow.com/questions/436198/what-is-an-alternative-to-execfile-in-python-3-0

This link says you need to navigate to the python script directory using powershell, start terminal session, then do:
<pre class="lang-py prettyprint prettyprinted"><code><span class="kwd">exec</span><span class="pun">(</span><span class="pln">open</span><span class="pun">(</span><span class="str">"./pythonscript.py"</span><span class="pun">).</span><span class="pln">read</span><span class="pun">())</span></code></pre>