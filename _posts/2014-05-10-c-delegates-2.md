---
ID: 718
post_title: c-delegates
author: admin
post_excerpt: ""
layout: post
permalink: http://localhost:8000/?p=718
published: true
post_date: 2014-05-10 00:00:00
---
What if you want the output of one method, to be treated as an input parameter for another method? i.e. you want to do something similar to linux bash piping in the world of c#, but instead of piping from one command, to another, you are piping from one method to another.

Here is an example

[csharp]

using System; using System.Collections.Generic; using System.Linq; using System.Text; using System.Threading.Tasks;

namespace DelegateDemo { class Program { static void Main(string[] args) { // Step 5 // Here we create an instance of the // Mediatester class, using it's default constructor class. // We use this object to access the RunTest method // later on. MediaTester ObjectContainingDelegate = new MediaTester();

            // Step 6
            // Here we create ordinary instances
            // of the MP3Player and MP4Player 
            // classes
            MP3Player mp3 = new MP3Player();
    
            // Step 7
            // We now want to create a reference instance
            // to this object's, PlayMP3File() method,
            // in the form of a delegate:
            MediaTester.TestMedia mp3Delegate = new MediaTester.TestMedia(mp3.PlayMP3File);
            // This syntax is a bit unusual because it is similar to the syntax 
            // you use to create an instance of a class, but in this case 
            // you use it to create a delegate.
            // we use this syntax because we need to tell c# where the delegate is located, so that it can find out
            // it's requirement (i.e. what behaviours matching methods needs to have, e.g. output parameter of integer)
            // as well as the actual object and it's method that we want to reference. 
    
            // Step 8
            // Now we do the same thing for the 
            // MP4Player class:
            MP4Player mp4 = new MP4Player();
            MediaTester.TestMedia mp4Delegate = new MediaTester.TestMedia(mp4.PlayMP4File);
    
            // step 9
            // Here the RunTest method requires a delegate as a input parameter,
            // when it calls on the delegate, the delgate in turn runs the method
            // against it's object, and retrieves the parameter output, which it
            // in turn passes into the RunTest as an input parameter. 
            // After which the RunTest method starts it's execution. 
            ObjectContainingDelegate.RunTest(mp3Delegate); // This line executes &quot;mp3.PlayMP3File()&quot; as part of it's execution. 
            ObjectContainingDelegate.RunTest(mp4Delegate); // This line executes &quot;mp4.PlayMP4File()&quot; as part of it's execution. 
    
        }
    }
    
    public class MediaTester
    {
        // Step 1
        // Here we created a delegate called &quot;TestMedia&quot;.
        // This delegate can act as delegate for to pipe  
        // any methods that requires no-input but gives out an 
        // integer output parameter. 
        public delegate int TestMedia();
    
        // Step 2
        // Here we create a method, that accepts an input 
        // parameter in the form of a &quot;TestMedia&quot; delegate. 
        public void RunTest(TestMedia testDelegate)
        {
            // Her we capture the output of the delegate into 
            // an integer variable. This should work since,
            // we defined that the TestMedia is a reference 
            // for any methods that gives an integer output. 
            int result = testDelegate();
            if (result == 0)
            {
                Console.WriteLine(&quot;Works!&quot;);
            }
            else
            {
                Console.WriteLine(&quot;Failed.&quot;);
            }
        }
    }
    
    // // Step 3
    // This is an ordinary class the consists 1 method called PlayMP3File,
    // which happens to fit the input/output parameter requirements of the  
    // &quot;TestMedia&quot; delegate
    class MP3Player
    {
        public int PlayMP3File()
        {
            Console.WriteLine(&quot;Playing MP3 file...&quot;);
            return 0;
        }
    
    }
    
    // Step 4
    // This is an ordinary class the consists 1 method called PlayMP4File,
    // which happens to fit the input/output parameter requirements of the  
    // &quot;TestMedia&quot; delegate 
    class MP4Player
    {
        public int PlayMP4File()
        {
            Console.WriteLine(&quot;Playing MP4 file...&quot;);
            return 1;
        }
    
    }

}

[/csharp]

This outputs:

![][1] 
A delegate essentially act's as a placeholder for a method that matches it's reguirements. See:

<http://stackoverflow.com/questions/2019402/when-why-to-use-delegates>

<http://stackoverflow.com/questions/31497/where-do-i-use-delegates> (contains a good analogy of the president of the united states)

 [1]: data:image/png;base64,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