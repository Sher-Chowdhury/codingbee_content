---
ID: 41
post_title: 'c# &#8211; Generics: Queue'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/c/c-generics-queue
published: true
post_date: 2014-05-10 00:00:00
---
Queues are like lists, but works on the basis of first-in, first-out, here's an example:

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
			// here we create an empty queue that we define to only hold
			// integers.
			Queue&lt;int=> MyQueue = new Queue&lt;int=>();
			
			// Here we add the first number (57) to the queue, using the 
			// the Enqueue method. 
			MyQueue.Enqueue(57);
			
			// Here we add another number to the queue
			MyQueue.Enqueue(101);

			// Here we add another number to the queue 
			MyQueue.Enqueue(18);

			// Here we add another number to the queue
			MyQueue.Enqueue(123);			
			
			
			
			Console.WriteLine(MyQueue.Dequeue());
			// The Dequeue method does 2 things,
			// - it outputs the next item in the queue
			// - it deletes removes the item from the queue
			
			Console.WriteLine(MyQueue.Peek());
			// here we just access the next item without 
			// dequeueing it.

			Console.WriteLine(MyQueue.Dequeue());
			
			
			// You can also view all the items in the queue like this:
			Console.WriteLine(&quot;Here are the remaining items in the list:&quot;);
			
			foreach (int number in MyQueue)
			{
				Console.WriteLine(number);
			}
		}
    }	
}
[/csharp]