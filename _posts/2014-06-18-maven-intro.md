---
ID: 122
post_title: 'Maven &#8211; Intro'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/maven-intro
published: true
post_date: 2014-06-18 00:00:00
---
Maven is basically a "build tool" that:
<ul>
	<li>always produces once artifact...e.g. a jar file, or a war file or a zip file</li>
	<li>manages dependencies (the main reason for using maven)</li>
</ul>
&nbsp;

Maven key selling point:

&nbsp;
<ul>
	<li>Maven lets you recreate your build for "any environment", and not needing to change settings for each environment. e.g. you can deploy to windows or linux environments.</li>
</ul>
&nbsp;

&nbsp;

&nbsp;

With maven, you have to organize all your code files in a certain folder structure, and the folders need to have certain names, this includes:

&nbsp;


1.0 src   (top level folder)

2.0 src\main   (second level folder)

3.0 src\main\java     (third level folder, this is where your source code will live, in form of filename.java)

&nbsp;

Since we are using the maven   folder structure, maven will know that it needs to compile any classes in src/main/java folder.


<strong>The Pom.xml file</strong>

The core part to Maven is the pom.xml. You need to create and place the pom.xml file In the top level folder (i.e. in the src folder), you need to create a pom.xml file (maven looks for this file when it's building the application). "POM" stands for project object model. This xml file is a bit rigid, like a template, and you need to fill out the mandatory fields.

<pre>
<project>
    <groupId>net.codingbee</groupId>
    <artifactId>HelloWorld</artifactId>
    <version>1.2.0</version>
    <modelversion>4.0.0</modelversion>
    <packaging>jar</packaging>
</project>
</pre>

Here is a summary of what the various tags means:

project - this is the top level of the xml  which houses the object's model.

groupid - this is the name of the company that owns the application. It is usually the company's website name, in reverse order.

artifactId -this is usually the name of the application, which in turn is the name of the project.

version - the version of the app

modelversion - the version of pom xml structure. This is not likely to change that much.

packaging - this tells maven to what the end product packaging type is. For java, it is usually jar/war/ear files, and for windows it can be msi files.

&nbsp;






&nbsp;

After you have created the project with some code in it, you can then open up powershell in admin mode, then navigate to the folder that contains the pom.xml file. then type:

maven clean

The first time you run this command, it will download lots of stuff which is a one off task. The main purpose of "clean" is to prepare the environment.

Then run:

mvn compile

This command will also download other stuff, but this again is a one time thing. The compile command will create a "target" folder which sits alongside the src folder.

Under the target folder, you'll find the "classes" folder. Inside this, you'll find your compiled code. for java, you should have a file with the "class" extension e.g.   "filename.class". You can then run this class like from the command line like this:

java helloworld

Note: you have to be in that directory and you can omit the "class" extension.

You might have a bunch of class, files that you need to "package" into a single file, in Java these packaged files can be ".jar" files. You can then instruct this like this:

mvn package

The jar file can be found in the target folder. the jar file's name has the following format:

{artifactid}-{version}.{packaging}

&nbsp;

<strong>  Other languages</strong>

When you are working in a java project, then your main source folder is:

src\main\java

However if you are working on an language type, e.g. groovy, then you can configure maven to look at the following instead:

src\main\groovy

&nbsp;

<strong>Testing</strong>

You can place automated unit tests here:

/src/test/java

Note, this folder is only for automated unit tests, and not system/blackbox testing.

&nbsp;

<strong>  More about the target folder</strong>

This is where everything gets compiled to and packaged. So in this folder you'll find:

"classes" folder - this contains the compiled code

projectname-version.jar - this is the packaged artifact.

"test-classes" folder - this contains the compiled automated unit tests.

"maven-archiver" folder - not sure

surefire folder.

&nbsp;

<strong>More about the pom file</strong>

The pom.xml file is made up 4 parts:
<ol>
	<li><strong>Project Information</strong>
<ol>
	<li>groupid</li>
	<li>artifactid</li>
	<li>version</li>
	<li>packaging - e.g. jar</li>
</ol>
</li>
	<li><strong>Dependencies</strong> - artifacts we want to use inside our application.
<ol>
	<li>Direct dependencies used in our application</li>
</ol>
</li>
	<li><strong>Build</strong> - this is about:
<ol>
	<li>what plugins we want to use.</li>
	<li>Directory structure info   (in case you wan to override the maven default structure)</li>
</ol>
</li>
	<li>Repositories - location where we downlaod artifacts from. Initially be default maven downloads from the "central maven repo", e.g. if you want to use a custom-made bespoke artifact.</li>
</ol>
&nbsp;

<strong>Quick overview of dependencies</strong>
<ul>
	<li>These are what we want to use in our application.</li>
	<li>dependencies are imported by their naming convention, this means you need to know the artifactid, groupid, and version</li>
	<li>adding dependencies are quite simple.</li>
</ul>
Before you can add a dependency you need to know 3 things, the dependencies:
<ol>
	<li>groupid</li>
	<li>artifactid</li>
	<li>version</li>
</ol>
Once you know all this, you then add the dependencies at the bottom of the pom file (after the project section), like this:

&nbsp;

[xml]
&lt;dependencies=>
    &lt;dependencies=>
        &lt;groupid=>commons-lang&lt;/groupid=>
        &lt;artifactid=>commons-lang&lt;/artifactid=>
        &lt;version=>2.1&lt;/version=>
    &lt;/dependencies=>
&lt;/dependencies=>
[/xml]

<strong>
Maven has 5 core commands</strong>
<ol>
	<li><strong>Clean</strong> - this deletes any target folder as well as any generated files (e.g. exe)</li>
	<li><strong>Compile</strong> - this compiles the source code, generate files, creates the target folder and populates it.</li>
	<li><strong>package</strong> - by default this will run the "compile" command first before it starts to package it. It also runs any unit tests.</li>
	<li><strong>install</strong> - this runs the "package" command and then copy/paste it in your local repo. It doesn't actually install the artifact on a target machine. The default local repo is located at "c:\users\username\.m2\repository"</li>
	<li><strong>Deploy</strong> - this runs the "install" command, to save packaged artifact in your local repository, after that the deploy command copy&paste it to the central corporate repo. the corporate repo can be as simple as fileshare server, or something else.</li>
</ol>
&nbsp;

If you want to run the "clean" command followed by the "package" command, then you can simply do:

<pre>
mvn clean package
</pre>

<strong>Using the "Install" command to save to a local repo</strong>

As mentioned earlier, when you run the install command, your packaged artifact gets copied from the target folder to to your local repo, which by default is located at:

c:\users\username\.m2\repository

In this folder the exact location where the packaged artifact stored is based on the following convention:

c:\users\username\.m2\repository\{groupid}\{artifactid}\{version} 

Also note, that the groupid is broken down further based on the dot notation. For example in the above helloWorld pom.xml, we would have:

c:\users\username\.m2\repository\net\codingbee\HelloWorld\1.2.0

Directly under this directory you should find the file:

HelloWorld-1.2.0.jar

Hence the fully path to the jar file in the local repo is: 


c:\users\username\.m2\repository\net\codingbee\HelloWorld\1.2.0\HelloWorld-1.2.0.jar


<strong>Over-riding the default conventions</strong>
Here's a reminder of how our example pom.xml file looks:

<pre>
<project>
    <groupId>net.codingbee</groupId>
    <artifactId>HelloWorld</artifactId>
    <version>1.2.0</version>
    <modelversion>4.0.0</modelversion>
    <packaging>jar</packaging>
    <dependencies>
        <dependency>
            <groupid>commons-lang</groupid>
            <artifactid>commons-lang</artifactid>
            <version>2.1</version>
        </dependency>
    </dependencies>
</project>
</pre>

So far we have looked at two key sections of the pom file.

The first section is the domestics, and give the general info, and it is made up of the tags groupid, artifactid, version, modelversion, packaging. This section isn't enclosed in it's own tags but nonetheless these tags makes up section in it's own right. This section is also mandatory. 

The second section that we looked at is "dependencies". This section is optional, and is only used as and when needed. 

However there is another section that you can add after the dependencies section, and that is the "build" section. This section is used if you want over-ride some of the maven defaults. For instance, in the above example if you run the "mvn clean package" command, you will end up with the packaged artifact with the filename of:

HelloWorld-1.2.0.jar
 
This is the default naming convention applied by Maven, however if you actually want your packaged file to be called "HelloWorld-beta.jar", then you can do this by introducing the build section and adding the "finalname" entry in the build section:


<pre>
<project>
    <groupId>net.codingbee</groupId>
    <artifactId>HelloWorld</artifactId>
    <version>1.2.0</version>
    <modelversion>4.0.0</modelversion>
    <packaging>jar</packaging>
    <dependencies>
        <dependency>
            <groupid>commons-lang</groupid>
            <artifactid>commons-lang</artifactid>
            <version>2.1</version>
        </dependency>
    </dependencies>
    <build>
        <finalname>HelloWorld-beta</finalname>
    </build>
</project>
</pre>




&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;