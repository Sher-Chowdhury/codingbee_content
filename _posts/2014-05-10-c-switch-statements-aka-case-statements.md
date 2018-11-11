---
ID: 28
post_title: 'c# &#8211; Switch statements (aka case statements)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/c/c-switch-statements-aka-case-statements
published: true
post_date: 2014-05-10 00:00:00
---
Here is a quick switch statement demo:

[csharp]
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace SwitchStatementDemo
{
    class Program 
    {
        static void Main(string[] args)
        {
            Console.Write(&quot;enter a number: &quot;);
            //the parse converts the input into an integer.
            int input = int.Parse(Console.ReadLine());
        
            switch (input)
            {
                case 1:        // this is how to do an &quot;or&quot; in switches.
                case 2:
                    Console.WriteLine(&quot;You picked 1 Or 2&quot;);
                    break;
                case 3:
                    Console.WriteLine(&quot;You picked 3&quot;);
                    break;
                case 4:
                    Console.WriteLine(&quot;You picked 4&quot;);
                    break;
               default:
		    Console.WriteLine(&quot;You picked a non-matching number&quot;);
                    break;
            }
            
            Console.WriteLine(&quot;Switch statement has ended&quot;);
        }
    }
}

[/csharp]