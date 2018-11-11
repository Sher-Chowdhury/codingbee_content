---
ID: 111
post_title: 'Jenkins &#038; PowerShell &#8211; Create Jenkins environment variables during a job run'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/jenkins/jenkins-powershell-create-jenkins-environment-variables-during-a-job-run
published: true
post_date: 2014-06-05 00:00:00
---
In <a href="http://www.amazon.co.uk/gp/product/1784390089/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&camp=1634&creative=6738&creativeASIN=1784390089&linkCode=as2&tag=codi0f-21">Jenkins</a> there are a set of default <a href="https://wiki.jenkins-ci.org/display/JENKINS/Building+a+software+project#Buildingasoftwareproject-JenkinsSetEnvironmentVariables">jenkins environment variables</a> that you can call from within a Powershell Build step. You can add a powershell script into a job's build step with the used of the <a href="https://wiki.jenkins-ci.org/display/JENKINS/PowerShell+Plugin">Jenkins Powershell plugin</a>. The Powershell build step can then access a jenkins job's build parameter, under the $env scope, that is::


<pre>
 if ($env:JenkinsParameter -eq $null){
        "ERROR: Cannot find value for 'JenkinsParameter'"
        exit 1
    }
 $JenkinsParameter = $env:JenkinsParameter
</pre>


You can also access <a href="https://wiki.jenkins-ci.org/display/JENKINS/Building+a+software+project#Buildingasoftwareproject-JenkinsSetEnvironmentVariables">Jenkins environment variables</a> in the same way, E.g. if you want to access the "Job_name" envrinment variable, then you do:


<pre>$JOB_NAME= $env:JOB_NAME
write-host $JOB_NAME</pre>


Also in your powershell script you can create <a href="http://codingbee.net/tutorials/powershell/powershell-environment-variables/">environment variables</a> that can be accessible by any latter powershell build steps. Also if you set the environment at the "machine" scope than these variables will be available by powershell build steps in different jenkins jobs. But that's not good practice because you want your Jenkins jobs self contained.  

However "powershell environment variables" are not accessible by any other types of build step (e.g. a python build step). You can only pass them from one powershell build step to any other successive powershell build steps that are within the same Job. 

The only way round this issue to create a variable as "Jenkins environment variable" (aka a jenkins job parameter that gets set midway through the job's run). This is possible with the help of the <a href="https://wiki.jenkins-ci.org/display/JENKINS/EnvInject+Plugin">EnvInject plugin</a>.

It is actually really easy to do. The way it works is that your powershell build step actually outputs all the variables to a file using the key=value syntax, e.g. here we created a file falled "env.properties":

<pre>
PS C:\> "message=HelloWorld" | Out-File env.properties -Encoding ASCII
</pre>

Straight after the powershell build step we add a new "inject" build step:

&nbsp;

&nbsp;

<a href="http://codingbee.net/wp-content/uploads/2014/06/Jenkins-inject-plugin.png"><img class="alignnone size-full wp-image-1609" src="http://codingbee.net/wp-content/uploads/2014/06/Jenkins-inject-plugin.png" alt="Jenkins-inject-plugin" width="648" height="218" /></a>

&nbsp;

Then do:

&nbsp;

<a href="http://codingbee.net/wp-content/uploads/2014/06/Jenkins-inject-plugin2.png"><img class="alignnone size-full wp-image-1610" src="http://codingbee.net/wp-content/uploads/2014/06/Jenkins-inject-plugin2.png" alt="Jenkins-inject-plugin2" width="581" height="268" /></a>

&nbsp;

Finally you can append another powershell build step and call this new jenkins environment variable like this:

&nbsp;

<pre>
write-host "variables have been injected"
write-host "the message is $env:message"
</pre>

Note: This, this technique of using envinject plugin isn't limited to powershell, the equivalent should work with other languages e.g. with python and ruby. 

Note: you can also pass the variables stored in env.properties to other jobs by triggering them from the current job. You can do because these types of build steps natively supports a properties file, e.g. :

<a href="http://codingbee.net/wp-content/uploads/2014/06/buildstep-trigger-anotherjob.png"><img src="http://codingbee.net/wp-content/uploads/2014/06/buildstep-trigger-anotherjob.png" alt="buildstep-trigger-anotherjob" width="717" height="511" class="alignnone size-full wp-image-1613" /></a>

&nbsp;
here's an example of a post build step triggering another jenkins job:

<a href="http://codingbee.net/wp-content/uploads/2014/06/jenkins-postbuildstep.png"><img src="http://codingbee.net/wp-content/uploads/2014/06/jenkins-postbuildstep.png" alt="jenkins-postbuildstep" width="1029" height="628" class="alignnone size-full wp-image-1614" /></a>
&nbsp;


Hence this env.properties file has two possible uses, it creates new environment variables midway through current jobs (using the EnvInject) plugin, and it can be used as an input file for triggering other jenkins jobs. 


<strong>Warning</strong>: You might have problems getting your powershell script working becuase of "second-hop"/remoting related issues. The way I have managed to fix this is by not using the jenkins powershell plugin. Instead I ran the powershell script inside an ordinary "execute windows batch command build step":

<a href="http://codingbee.net/wp-content/uploads/2014/06/windows-batch-command-to-run-powershell-script-in-jenkins.png" rel="attachment wp-att-6400"><img src="http://codingbee.net/wp-content/uploads/2014/06/windows-batch-command-to-run-powershell-script-in-jenkins.png" alt="windows-batch-command-to-run-powershell-script-in-jenkins" width="947" height="269" class="alignnone size-full wp-image-6400" /></a>  

Note, to do the above, you first need to save your powershell code into a .ps1 file and add it to your code repo, in order to make it available via your jenkins workspace.