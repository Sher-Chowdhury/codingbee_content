---
ID: 42
post_title: 'c# &#8211; Generics: Stack'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/c/c-generics-stack
published: true
post_date: 2014-05-10 00:00:00
---
Stacks are practically the same thing as Queues, but works on the basis of last-in, first-out.

Here is the stack's reference page:

http://msdn.microsoft.com/en-us/library/3278tedw(v=vs.110).aspx



It basically works in the opposite order of how a queue works. Here's an example:

[csharp]
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Queue
{
    class Program
    {
        static void Main(string[] args)
        {
			// here we create an empty stack that we define to only hold
			// integers.
			Stack&lt;int=> Stack = new Stack&lt;int=>();
			
			// Here we add the first number (57) to the stack, using the 
			// the Enqueue method. 
			Stack.Push(57);
			
			// Here we add another number to the queue
			Stack.Push(101);

			// Here we add another number to the queue 
			Stack.Push(18);

			// Here we add another number to the queue
			Stack.Push(123);			
			
			
			
			Console.WriteLine(Stack.Pop());
			// The Pop() method does 2 things,
			// - it outputs the last item added to the stack
			// - it  removes the item from the stack
			
			Console.WriteLine(Stack.Peek());
			// here we just access the next item without 
			// dequeueing it.

			Console.WriteLine(Stack.Pop());
			
			
			// You can also view all the items in the queue like this:
			Console.WriteLine(&quot;Here are the remaining items in the list:&quot;); 
			
			foreach (int number in Stack)
			{
				Console.WriteLine(number);
			}
		}
    }	
}
[/csharp]