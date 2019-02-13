---
ID: 634
post_title: c-an-overview-of-commonly-used-variables
author: admin
post_excerpt: ""
layout: post
permalink: http://localhost:8000/?p=634
published: true
post_date: 2014-05-10 00:00:00
---
Previously we looked at why variables are categorized as either "value types", or "reference types" (primarily for memory reasons).

Now lets take a look at these variables in action, starting with the string variable.

# String variables

We already encountered string variables when we created the "message" string variable in our hello world program.

You can also Concatenate existing string variables to form new string variables:

[csharp] using System; class ConcatenateStrings { static void Main() { string firstname = "James" ; string lastname = "Bond" ; string fullname = firstname + " " + lastname; Console.WriteLine ("My name is " + lastname + ", " + fullname );

                string country = &quot;uk&quot;;
                string CounterUpperCase = country.ToUpper();
                Console.WriteLine(fullname + &quot; lives in the &quot; + CounterUpperCase );
    }

} [/csharp]

Line 8 - here we are using a non-static method called ToUpper(), which is a member of the string class. We will cover more about static and non-static methods later, but for now it just gives early sight of how methods are used.

# Boolean

This variable can only have one of 2 values, "true" or "false". Booleans are commonly used to store the result of something we check. o

[csharp] using System; class BooleanExample { static void Main() { bool AboolVariable = true; bool AnotherBoolVariable = false; Console.WriteLine("First boolean's value is: " + AboolVariable); Console.WriteLine("Second boolean's value is: " + AnotherBoolVariable);

        int x = 1;
        int y = 2;
        int z = 1;
    
        Console.WriteLine (&quot;x is equal to:&quot; + x);
        Console.WriteLine (&quot;y is equal to:&quot; + y);
        Console.WriteLine (&quot;z is equal to:&quot; + z);
    
        bool FirstComparison = (x == y);  // True
        Console.WriteLine (&quot;Is x equal to y: &quot; + FirstComparison);
    
        bool SecondComparison = (x != y);
        Console.WriteLine (&quot;Is x not equal to y: &quot; + SecondComparison);
    
        bool ThirdComparison = (x == z);
        Console.WriteLine(&quot;Is x equal to z: &quot; + ThirdComparison);
    
        bool FourthComparison = (x &lt; y);
        Console.WriteLine(&quot;Is x equal to y: &quot; + FourthComparison);
    
        bool FifthComparison = (x =>= z);
        Console.WriteLine(&quot;Is x greater than or equal to z: &quot; + FifthComparison);
    }

}

[/csharp]

In the above program we have taken our first look at comparison operators, we will cover more about comparison operators later.

# Arrays

An array is essentially a list of values grouped together into list.

[csharp] using System; class ArrayExample { static void Main() { string[] fruits = new string[5]; fruits [0] = "a"; fruits [1] = "e"; fruits [2] = "i"; fruits [3] = "o"; fruits [4] = "u";

        for (int i = 0; i &lt; fruits.Length; i++)
        Console.WriteLine (vowels[i]);
    }

} [/csharp]

Here is another way to declare an array:

[csharp] using System; class FruitsArray { static void Main() { string[] fruits = {"apple","bananas","oranges","mangoes"};

        Console.WriteLine (fruits[0]); // this prints &quot;apple&quot;
        Console.WriteLine (fruits[1]); // this prints &quot;banana&quot;
        Console.WriteLine (fruits[2]); // this prints &quot;oranges&quot;
    }

} [/csharp]

# Integers

Here are some examples of integer, integers are often used for doing maths:

[csharp] using System; class LotsOfNumbers { static void Main() {

        int FirstNumber = 12;
        int SecondNumber = 3;
        int SumOfBothNumbers = FirstNumber + SecondNumber ;
    
        Console.WriteLine(&quot;The first integer is:   &quot; + FirstNumber);
        Console.WriteLine(&quot;The second integer is:   &quot; + SecondNumber);
        Console.WriteLine(&quot;Adding up both numbers gives:   &quot; + SumOfBothNumbers);
        Console.WriteLine(FirstNumber + &quot; minus &quot; + SecondNumber + &quot; gives:   &quot; + (FirstNumber - SecondNumber) );
        Console.WriteLine(&quot;Multiplying both numbers gives:   &quot; + (FirstNumber * SecondNumber) );
        Console.WriteLine(&quot;Dividing &quot; + FirstNumber + &quot; by &quot; + SecondNumber + &quot; gives:   &quot; + (FirstNumber / SecondNumber) );
    }

} [/csharp]