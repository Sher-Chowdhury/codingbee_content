---
ID: 30
post_title: 'c# &#8211; Declare variables outside if-else statement scope'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/c/c-declare-variables-outside-if-else-statement-scope
published: true
post_date: 2014-05-10 00:00:00
---
If you declare (and initialize) a variable within an if-else statement, then that variable's value will only exist within that if-else statement. However if you want to initialize (i.e. assign a value to) the variable within the if-else statement, but want to make the variable accessible outside the if-else scope, then you need to first declare the variable, in the highest most scope where you want to make the variable accessible, and then initialize it within the scope, here's an example:

[csharp]
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

class Program
    {
        static void Main(string[] args)
        {

			string ContainsSeedsSentence;   // Have to declare this outside if-else statement.
            if(true)
            {
                Console.WriteLine(&amp;quot;True statement run&amp;quot;);
                ContainsSeedsSentence = &amp;quot;This fruit contains seeds.&amp;quot;;
                //Console.WriteLine(ContainsSeedsSentence);
            }
            else
            {
                Console.WriteLine(&amp;quot;False statement run&amp;quot;);
                ContainsSeedsSentence = &amp;quot;This fruit is seedless.&amp;quot;;
                //Console.WriteLine(ContainsSeedsSentence);
            }

            Console.WriteLine(ContainsSeedsSentence);
        }
    }
[/csharp]