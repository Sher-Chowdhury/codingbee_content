---
ID: 94
post_title: 'PowerShell &#8211;  Creating a GUI tool'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-creating-a-gui-tool
published: true
post_date: 2014-05-11 00:00:00
---
Chapter 23 - Creating a GUI tool, Part 1: the GUI


You can create a gui interface in Powershell by making using of the .net library. In .net there are actually 2 GUI systems available:

  - Windows Forms (aka WinForms)
  - Windows Presentation Framework (aka WPF) - this is newer, more modern system, but hasn't been introduced to replace winforms

Out of the 2 we will be using winforms, because there is no drag-and-drop powershell IDE available for building forms using WPF, however there 
is one for winforms, called Sapien Powershell Sudios 2012:

http://www.sapien.com/software/powershell_studio
 
For the time being, this only supports winforms and not wpf:

http://www.sapien.com/blog/2010/10/18/does-primalforms-use-wpf/
https://www.sapien.com/forums/viewtopic.php?f=8&t=6628

To summarise, powershell studio is the only software of its kind that lets you create a powershell gui using a gui. 

## Winforms crash course - start ##
Winforms is based on the concept of forms (aka windows). Each windows/form can house a number of items, such as:

	- checkboxes
	- text boxes
	- buttons

Each of these items are called "Controls". 

Each of these "controls" have a variety of "properties" which are mainly used to customise the various aspects of that control, including:
	- appearance (e.g colour, font type ...etc )
	- behaviour (e.g. if check box, then what is the default state, checked or unchecked, also is it enabled or greyed out)
	- layout (where is it placed, in the context of the main window)
	

Controls are basically a kind of object, a bit like powershell objects. Hence a control's properties are a bit like a powershell object's properties. 

However each "control" also have something called "events" (indicated by the lightning icon). Events are a bit like a status of an object, e.g. the status of a service can either be 
stopped or running. However a better description of an event is that an event is essentially a notification to the user. 

E.g. Lets say we a have a control that is a checkbox. If that checkbox is unticked and you tick on it, then that checkbox "notifies" you that it
has been activate by displaying a tick. These ticked/unticked statuses are referred to as "events".  

When each event is activated....then this triggers something called "event handlers". Event handlers are essentially empty containers where 
you can add your powershell code in (or maybe other e.g. c#). This is the feature that makes your gui forms interactive. 

In powershell studio, if you right click a control, and select add event, then you will end up adding an event handler to the code, this event handler has the following syntax:

	<control's name>_<event's name>={
		# here you enter what you want to happen as soon as the event is triggered 
	}

e.g. for button, it could be something like:

	$button1_Click={
		#here is what needs to be done. 
		$testvar = 5
	
	}

For more sophisticated controls, e.g. a treeview (which is a collapsible, hierarchial checkbox tree), the event-handler's syntax could look slightly different:

	<control's name>_<event's name>=[a.name.of.a.handler]{
		# here you enter what you want to happen as soon as the event is triggered 
	}

e.g. :

	$treeview1_AfterSelect=[System.Windows.Forms.TreeViewEventHandler]{
		# here you write your code, e.g.:
		$testvar = 5
	}

  
  
When doing debugging, there are a few useful techniques:
	- write-host (if you want to output a simple variale), note you can also do: write-host ($object | gm) ..... but that isn't so good because
	  it outputs in a really long string. instead, a better option is to use:
	- write-debug (covered earlier)

 
 
For more reference info about winforms, checkout:

http://msdn.microsoft.com/en-us/library/cc656767


### Winforms crashcourse - end  ###


Note, it is possible to create a ps gui by handwriting all the code, however this is very time consuming and it is much 
faster to have the powershell studio IDE software autogenerate the code for you. 


  
A tool can be made up of several forms. And each form is represented by a function that resides in the root parent script. If the 
forms name is something like:

results.pff

then the correspoding function name conforms to the following format:

call-results_pff		# essentially, it adds the "call-" prefix and replaces the period, "." with a underscore, "_"

You can then place this in the button-click of one form to trigger this form.