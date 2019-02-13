---
ID: 719
post_title: >
  c-embed-variables-in-a-string-using-the-substition-technique
author: admin
post_excerpt: ""
layout: post
permalink: http://localhost:8000/?p=719
published: true
post_date: 2014-05-10 00:00:00
---
Previously in the boolean chapter we saw:

[csharp]

using System;

class BooleanDemo { static void Main() { bool myFirstBoolean = 3 + 2 == 5; Console.WriteLine(myFirstBoolean.ToString());

        bool mySecondBoolean = 3 + 2 => 5;
        Console.WriteLine(mySecondBoolean.ToString());
    
        // Here we use the 'and', &quot;&amp;&amp;&quot; operator
        bool BothAreTrue = myFirstBoolean &amp;&amp; mySecondBoolean ;
        Console.WriteLine(&quot;Are both true: &quot; + BothAreTrue.ToString() + &quot; That's becuase one of them is false&quot;);
    
        // Here we use the 'or', &quot;||&quot; operator
        bool IsOneTrue = myFirstBoolean || mySecondBoolean ;
        Console.WriteLine(&quot;Are both true: &quot; + IsOneTrue.ToString() +  + &quot; That's because only of them them needs to be true&quot;);
    
    }

} [/csharp]

We can use a technique called substitution as a shorthand, and simpler way to embed objects in strings. It works by embedding curly-braced-numbers in the string, e.g.:

[csharp]

using System; class SubstitionTechnique { static void Main() { bool myFirstBoolean = 3 + 2 == 5; Console.WriteLine(myFirstBoolean.ToString());

        bool mySecondBoolean = 3 + 2 => 5;
        Console.WriteLine(mySecondBoolean.ToString());
    
        // Here we use the 'and', &quot;&amp;&amp;&quot; operator
        bool BothAreTrue = myFirstBoolean &amp;&amp; mySecondBoolean ;
        Console.WriteLine(&quot;Are both true: {0}. That's becuase one of them is false&quot;, BothAreTrue);
    
        // Here we use the 'or', &quot;||&quot; operator        
        bool IsOneTrue = myFirstBoolean || mySecondBoolean ;
        Console.WriteLine(&quot;Are both true: {0}. That's because only of them them needs to be true&quot;, IsOneTrue);
    
    }

}  
[/csharp]

The linux Awk command works in a similar way. 

Also see:

<http://msdn.microsoft.com/en-us/library/fk49wtc1%28v=vs.110%29.aspx>