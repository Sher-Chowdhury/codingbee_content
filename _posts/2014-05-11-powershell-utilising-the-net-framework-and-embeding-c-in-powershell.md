---
ID: 93
post_title: 'PowerShell &#8211; Utilising the .NET framework and embeding c# in PowerShell'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-utilising-the-net-framework-and-embeding-c-in-powershell
published: true
post_date: 2014-05-11 00:00:00
---
Chapter 21 - Globalizing a function   (skipped because it looks at translating functions to other languages, e.g. french, spanish, etc)

Chapter 22 - Crossing the line: Utilising the .NET framework

.net is all about objects. First what is a class?:

class - this is a hunk of code that act as the template to create an object. You can use a class to create many instances of an object. An "instance". Here is how a class as an example:

########
PS C:\> $source = @"
public class BasicTest
{
    public static int Add(int a, int b)
    {
        return (a + b);
    }

    public int Multiply(int a, int b)						# notice that this is an "int", aka a "non-static method"
    {
        return (a * b);
    }
}
"@
###########

This .net class is stored in a powershell variable called "$source". The class itself is called "BasicTest"

For this to work, you need to define the class in: @"...."@

This class contains 2 "methods" (I think you can think of them as functions), aka known as "members", they are called:
	- Add (which accepts two parameters, 'a', and 'b')
	- Multiply (which accepts two parameters, 'a', and 'b')
	
Now to make ps recognise that $source is a class, you do:

PS C:\> Add-Type -TypeDefinition $source

Once you have done this, a new class by the name of "basictest" should now have been created. You should be able to access the "static" methods of this class, i.e. you can do this:

[BasicTest]::Add(4, 3)

Hence the basic syntax is:

{class's name}::{method's name}(parameter1,parameter2,...parameter-x)

Note: the syntax "::" is specifically used to access a "static" members of the class.

However you can't do this:

[BasicTest]::Multiply(4, 3)			# that's because multiply is an "int" and not a static method. 

If you want to use the "multiply" method, then you first have to create a generic powershell object from this class, i.e., you do this:

$basicTestPowerShell = New-Object BasicTest

PS C:\> $basicTestPowerShell | gm
   TypeName: BasicTest							# Notice what the object type
Name        MemberType Definition                    
----        ---------- ----------                    
Equals      Method     bool Equals(System.Object obj)
GetHashCode Method     int GetHashCode()             
GetType     Method     type GetType()                
Multiply    Method     int Multiply(int a, int b)    	# Notice that we now have the multiply method. But the "add" method is missing
ToString    Method     string ToString()             


Now you can acess the multiply method:

PS C:\> $basicTestPowerShell.multiply(4,3)
12

Note: In a class, you can have one or more static members, but I think you must always have at least (or exactly) 1 nonstatic member. 


You can also use gm list the members of classes, such as the the basictest class that we created using the add-type command:

PS C:\> [BasicTests] 

IsPublic IsSerial Name       BaseType                                                                                                        
-------- -------- ----       --------                                                                                                        
True     False    BasicTests System.Object                                                                                                   

PS C:\> [BasicTests] | gm
   TypeName: System.RuntimeType
Name                           MemberType Definition     
----                           ---------- ----------                         
Clone                          Method     System.Object Clone()                     
Equals                         Method     bool Equals(System.Object obj), bool Equals(type o) 
FindInterfaces                 Method     type[] FindInterfaces(System.Reflection.TypeFilter filter, System.Object 
.
.
.
etc.


PS C:\> [BasicTest] | gm -Static						# "-static" switch will only list the static methods. 
   TypeName: BasicTest
Name            MemberType Definition                                                         
----            ---------- ----------                                                         
Add             Method     static int Add(int a, int b)                                       
Equals          Method     static bool Equals(System.Object objA, System.Object objB)         
ReferenceEquals Method     static bool ReferenceEquals(System.Object objA, System.Object objB)




Classes interact with eachother



In the .net framework we have the following terminologies:

class: 	    This is basically a hunk of code that handles some specific set of related tasks. E.g. the "System.math" class provides 
			capabilities for doing arithmetics. Most classes have a multipart names like this which are seperated by periods. In the case of
			"system.math", it means that the "math" class belongs to the "system" namespace. i.e. 
			
Assemblies 	: (see def for class above) Assemblies are actually the physical files that contains compiled codel. If the essambly contains re-usable 
			 code, then they are dll files, if they are standalone apps, then they are exe files. 
			
namespace:	This is an "organisational" element for logically grouping classes together. Hence each class belongs to a namespace. 
			Analogy, a football player belongs to a team. Confusingly, classes are grouped together in another way using "assemblies". There is no
			strict relationship between classes and namespaces, i.e. an assembly can contain multiple namespaces, or just a subset of a namespace.  

instance:	this is like creating a new instance of a class. It is a bit like using a Microsoft word template to create a new doucment. 

###################### Detour start
Namespace and assemplies are quite similar, but the key difference is that namespace is the logically way to group classes together, whereas assemblies are the physical ways to group classes together. It is however common to have an entire namespace stored in the assembly that has the same name. But this is not always the case. 

To view a list of list of currently loaded assemblies, you have to do this with a .net class, (since a ps command isn't available to do this):

PS C:\> [System.AppDomain]::CurrentDomain.GetAssemblies()     # "GetAssemblies()" is a static method, hence this works. covered later. 

GAC    Version        Location                                                             
---    -------        --------                                                              
True   v2.0.50727     C:\WINDOWS\Microsoft.NET\Framework\v2.0.50727\mscorlib.dll 
False  v2.0.50727     C:\WINDOWS\system32\WindowsPowerShell\v1.0\powershell_ise.exe         
True   v2.0.50727     C:\WINDOWS\assembly\GAC_MSIL\System.Management.Automation\1.0.0.0__31bf3856ad364e35\System.Management.Automation.dll
.
.
.
etc

Now to add a new assembly to the current session, do:

PS C:\>  Add-Type -AssemblyName {assembly name}

Unfortunately there is no command for viewing all the available (loaded+nonloaded) assemblies. 

####################### Detour end



E.g. here's how we create a an instance of the system.string class:

$var = "hello"

$var | gm | Select-Object -First 1
   TypeName: System.String
Name  MemberType Definition                                             
----  ---------- ----------                                             
Clone Method     System.Object Clone(), System.Object ICloneable.Clone()


In fact, ALL objects outputed by powershell commands, are actually instances of .net classes!

Hence, the get-process command produces an instance of the System.Diagnostics.Process class. 

	
Each class comes with a collection of:

	- Properties
	- Methods
	- Events

These things are collectively called "members"...hence this is what the "get-member" commands naming comes from. 


All instances have access to these members. 


many classes

	


Useful link:
http://blogs.technet.com/b/heyscriptingguy/archive/2013/01/19/weekend-scripter-basics-of-net-framework-language.aspx 

add-type   	# this cmdlet lets you define a .NET Framework class in your Windows PowerShell session. 
			# You can then instantiate objects (by using the New-Object cmdlet) and use the objects, just as you would use any .NET Framework object.
			# see help pages for more info, especially for the first example. 




############ Detour - start

add-type   	# this cmdlet lets you define a .NET Framework class in your Windows PowerShell session. 
			# You can then instantiate objects (by using the New-Object cmdlet) and use the objects, just as you would use any .NET Framework object.
			# see help pages for more info, especially for the first example. 


############ Detour - end