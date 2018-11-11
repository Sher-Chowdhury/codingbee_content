---
ID: 580
post_title: 'Python &#8211; crash course'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/python/python-crash-course
published: true
post_date: 2016-05-11 00:00:00
---
To install python:

<pre>
$ yum install python
</pre>

indentation is important, since code blocks are not encased in any brackets whatsoever. 

to access python command line (aka repl) do:

<pre>
[root@ansibleclient01 ~]# python
Python 2.7.5 (default, Nov 20 2015, 02:00:19)
[GCC 4.8.5 20150623 (Red Hat 4.8.5-4)] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> a
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'a' is not defined
>>> 4
4
>>> 2 + 2
4
>>> 2 + 3
5
>>> x = abc
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'abc' is not defined
>>> x = 'abc'
>>> x
'abc'
>>>
</pre>

<code>ctrl+d</code> to exit the python terminal

<pre>
>>> print('hello world')
hello world
>>>
</pre>


Python comes included with the standard library. This library is:

https://docs.python.org/2/library/

This shows a list of modules you can 'import' into your scripts, without needing to install them separately (using pip utility).


To view help info for the 'math' module do:

<pre>
>>> help('math')
Help on module math:

NAME
    math

FILE
    /usr/lib64/python2.7/lib-dynload/math.so

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
</pre>

To use the math module's "sqrt" function, you do:


<pre>
>>> import math
>>> math.sqrt(81)
9.0
>>></pre>


Heres how to run a python script:


<pre>[root@ansibleclient01 ~]# cat helloworld.py
print("Hello World!!!")
[root@ansibleclient01 ~]# python helloworld.py
Hello World!!!
</pre>


You can import a python script, simply by using the import command, followed by the script's filename, but excluding the .py extension:


<pre>
[root@ansibleclient01 ~]# python
Python 2.7.5 (default, Nov 20 2015, 02:00:19)
[GCC 4.8.5 20150623 (Red Hat 4.8.5-4)] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import helloworldd
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ImportError: No module named helloworldd
>>> import helloworld
Hello World!!!
>>>
</pre>

This is useful if your python script has function that you want to make use of. E.g. let's say we have the following function:

<pre>
[root@ansibleclient01 ~]# cat helloworld.py
print("Hello World!!!")

def squaringnumber(x):
    return x * x
[root@ansibleclient01 ~]# python
Python 2.7.5 (default, Nov 20 2015, 02:00:19)
[GCC 4.8.5 20150623 (Red Hat 4.8.5-4)] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import helloworld
Hello World!!!
>>> helloworld.squaringnumber(9)
81
>>>
</pre>