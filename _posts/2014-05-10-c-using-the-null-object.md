---
ID: 44
post_title: 'c# &#8211; Using the &#8220;Null&#8221; object'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/c/c-using-the-null-object
published: true
post_date: 2014-05-10 00:00:00
---
Sometimes a method will ask for an input parameter, but that input parameter is optional. In these situation you can still trigger the method, without passing an input parameter, and instead pass in a "null" object in it's place:

[csharp]
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ExceptionExample
{
    class Program
    {
        static void Main(string[] args)
        {

            FirstMethod(&quot;hello world&quot;);
            // FirstMethod();           // this will cause an error because the &quot;firstmethod&quot; method must require
										// an input parameter in order to operate
            FirstMethod(null);

        }

        public static void FirstMethod(string message)
        {
            if(String.IsNullOrEmpty(message))
            {
                Console.WriteLine(&quot;There is no message that has been passed in&quot;);
            }
            else
            {
                Console.WriteLine(&quot;The message is: &quot; + message);
            }
            
        }

    }
}
[/csharp]