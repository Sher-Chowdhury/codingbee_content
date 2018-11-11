---
ID: 117
post_title: 'Python &#8211; The Standard Library'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/python/python-the-standard-library
published: true
post_date: 2014-06-14 00:00:00
---
Python comes with a large number of <a href="https://docs.python.org/3/library/functions.html">builtin functions</a>. However these alone only provides a limited functionality of python. The true power of python comes from the python's standard library. 

Python comes included with an extensive library of modules. modules extends python's features, in the same way as installing an app on smart phone, will extend what the smart phone can do. However unlike smart phone which requires downloading followed by installing and activating, you don't need to download python modules since they are already in the standard library which comes with python as standard. The only thing you need to do is activate the modules you want to using in your current python session, or inside your python script.

You use the "import" keyword to activate/access a module, like this:

import {module_name}

You can find a <a href="https://docs.python.org/3/library/"> list of all modules in the Python Standard Library</a> on the official website.

For example, if you want to do square roots of a number, then you need to use the "<a href="https://docs.python.org/3/library/math.html">math</a>" module:

[python]
=>=>=> import math
=>=>=> math.sqrt(81)
9.0
=>=>=> math.sqrt(81) * math.sqrt(25)
45.0
=>=>=>
[/python]

If you are familiar with oop languages, e.g. c#, then you can think of "math" as a class, and "sqrt" is a static-method of the class. Reminder, static-methods are method that you use without having to instantiate an object of that class. Hence the "." notation is the standar oop 

You can view help info for a module using the help() funtion:

[python]
=>=>=> help(math)
Help on built-in module math:

NAME
    math

DESCRIPTION
    This module is always available.  It provides access to the
    mathematical functions defined by the C standard.

FUNCTIONS
    acos(...)
        acos(x)

        Return the arc cosine (measured in radians) of x.

    acosh(...)
        acosh(x)

        Return the hyperbolic arc cosine (measured in radians) of x.

    asin(...)
        asin(x)

        Return the arc sine (measured in radians) of x.

    asinh(...)
        asinh(x)

        Return the hyperbolic arc sine (measured in radians) of x.

    atan(...)
        atan(x)

        Return the arc tangent (measured in radians) of x.

    atan2(...)
        atan2(y, x)

        Return the arc tangent (measured in radians) of y/x.
        Unlike atan(y/x), the signs of both x and y are considered.
[/python]

&nbsp;

If you want to view help for a specific method, rather than the whole module, then you can simply do:
&nbsp;
[python]
=>=>=> help(math.sqrt)
Help on built-in function sqrt in module math:

sqrt(...)
    sqrt(x)

    Return the square root of x.

=>=>=>
[/python]
&nbsp;

If you want, you can import method directly into the current workspace, which would allow you to use the method without needing to use it's fully drilled down name:

[python]
=>=>=> sqrt(81)                        # this will output an error message.
Traceback (most recent call last):
  File &quot;&lt;stdin=>&quot;, line 1, in &lt;module=>
NameError: name 'sqrt' is not defined
=>=>=> from math import sqrt
=>=>=> sqrt(81)
9.0
=>=>=>
[/python]

However this approach can be risky in case you already have a sqrt function in the current namespace, in which case there could be a conflict. If there is a namespace clash but you still want to impart a method into the namespace directly, then you can give the method you want to name an alternative name (a bit like an alias) during the import process, e.g. :

[python]
=>=>=> from math import sqrt as SquareRoot
=>=>=> SquareRoot(81)
9.0
=>=>=> 
[/python]

Note: everything in python is case sensitive. 


&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;