---
ID: 14
post_title: 'c# &#8211; Storing data inside objects (by using &#8220;fields&#8221;)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/c/c-storing-data-inside-objects-by-using-fields
published: true
post_date: 2014-05-10 00:00:00
---
Whenever you create an object (aka an instance of a class), you often want to store data within that object. You can think of this info as the object's metadata. For example, lets say want to have a class called "Employee", and you want to use that class to create "employee" objects, and each employee-object needs to store the following info:

firstname
age
DateJoined
Salary

Note: the words "instance" and "object" are synonyms.

In order to make this possible, you first need to write your Employee class to accept this info. After that, you can call on the class to instantiate the object and pass the metadata into it.

There are a number of ways to write your
<h3>1st Approach: Store the data directly in an object, using "Fields"</h3>
In this approach we have declared inside the employee class, that an employee-object can store 3 bits of data:
age,
firstname,
salary.

In C#, these items are referred to as "fields", and whenever you declare a new field, you have to also specify what type of data it can hold. For example in in case of the "age" field, we have specified that it can only hold integers (as indicated by the "int").

We also have to specify the access level in the form of <a href="http://msdn.microsoft.com/en-us/library/ms173121.aspx">access modifiers</a>. We will cover more about access modifiers later.

[csharp]
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp
{

    class Employee
    {
        public int age;
        public string firstname;
        public double salary;
    }

    class Program
    {
        static void Main()
        {
            // here we create a new object using the Employee class.
            Employee Dave = new Employee();

            Dave.age = 50;
            Dave.firstname = &quot;David&quot;;
            Dave.salary = 35461.32;
            Console.WriteLine(&quot;{0} is {1} years old and earns  £{2} per year.&quot;, Dave.firstname, Dave.age, Dave.salary);
        }
    }
}

[/csharp]

This will output:<img alt="" src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAkoAAABNCAIAAADW29xfAAAPO0lEQVR4nO2dz44dRxXGe/5ZLFGsGPMKrBFvwGPwBjwBT+JNdrBCQkEoyraVXcQigojIwaBIFoyQFxaWiIhIRmZhc+mpOuer75yq7tv3+vspivpWn/rOd05Xd92ZO56ZfvPZP3/7x3/97k///ujpfz7+8ruP//zdVy/vfvbzX0xCCCHE6aLtTQghxBmi7U0IIcQZ4m1v8zzP83wIK16aI0dk/h8D1QrlOkUoY60JAsZimk9MObzkpRKpo3grc1frM8EG/skU3vpn4hkdLO7dKdH+JHSAT96/OCZ4e5sXjzNzsR7DcknhsziVVjNHzJUdlTVnrd3PfqugzyGdsXjK+1mfObZZD80U0QXfXDzFS7xI8EhuSZM6RX9y/sWRaW5vS45t1sbzlvAMFroXQGY5+m2Q8DlVtzG/Hjydzdjzij06/E1dr1u+q96a93TMGHC78f6jOvUsz3/Uj9iUxPaGV0wx7i2FqE5zvKjLMw/wwpormNEHIol+hnR4kzjscCpar3kKmDdrxFMYAzhvoi7GD9D3/IfG5/tg84XtUL3RLJPTK6DjldzUDDlp6pj98fzn/IiNaH725t0J3poAp5hlkTteKnsOm3jxIAWfqHkbkP1s6pD6jE/vVLReb9w8rlPgkskmAJGiLlxa4rqY+rgo3n9Th/TfjK9tN2d5Tah1vM6DQW8KrsLTMSPN49p/1IzYjlE/WgKu7vLa1wekVDQeWwI+wXhPIuDfG8z1YdntqEkc1uwDo+P5X4ozYaFEU9Uc8xRTGjYW0se5TBH+GMt6VnE8mQJ4xjr1IEiXduJ5q33Whmv/no7YBZttb2CQWesg3kudWG0Jq2SWaL3euKeT1ifTzRZpHeCZDGvWBfqGIzGhnjP6Cf8JDwljy5hcinoi1uEvbo8Tz1vOf0//xeqssb0154L46LGnPzlLsEnoTkgoN93ipIn+RK02ZUmp5lwvIFRaZz/5cor4Tv3o9WIakiukHgHmzZdRnxOxHnBFeLzZzGbeUf7FkUlsb3NFPV5MBGvOW3nkONCf/OWOMYvyTqWVa5NMPwtLnlVPh/RsBpviaR1PvH7pmffqYsaxGbKudLH4VMI/cIVL8NwCP+apRF2kE9Kkd4rRwVJzZB3WOuKY6LeWiJ2jB4cQIoO2N7Fn9NZYCJFE25sQQogzBG9v671xNr9/DQKOy9g+gG/o45505lq+rPN644x+aMqu6PGc608ikRAiA9jeikfe2LzmI7UOGJs0B+hDwiGutDjb3wQgWJ9a/n/5kk+xn6vGk/NMblen2BAhzgRme1uD2WKlXJ143hKezSl4e+jsTNFbcisKJTW3yRMifR21vQmxaxLbm3lvL0f4274A6CfyhnSa403/uF5PZyK2HFIfJDVbVBynM5o60esC+gkGPQ9AhJlL1hsKFkJsDfkrlQ/x4FlQ3Pb4rl4G11JAnMzrxeeOzYqiTy4vHqTIJQLia5Tm6RxeeqWZPZ/vXz4zpnbYjAd+zLm4WFMfxHsdEEKsSPSrt+bjg3xSeI8ePgDnZR5zobqaJTRp6oxKBMS9MnHzyRSFjnldJueJv4z0bPPxZhWMH7JY0xI5lwwWQvQyfHsj8zbn8uJRn019MnXiaZWw2vlMrMVxWxJJQ/3xDByOD/8vDjwdEB+9jtFWrx0vhOjiFLc3Jm/zMUoeg7pyj0VcaU9LD/FAAefK5W32xxs0j4uWMs0ZdR1BjIl3KcmGCyHWhfnsrZhingLxJs2nQC0YzeudCo3jukIl42K9Uwllr7FTtSUUFCJM9miTC5P1QW0bp/DmMoMT7H+i8GgrhBArot9aIoQQ4gzR9iaEEOIM0fYmhBDiDCF/52T6Y4Orm8cXFw8SE8mka3yqQX58EsrbnLurz2ZGdfUMPnY6inkzqdfMsU0+4n130qghe4T8ycn0lbu6eXx5/TCxw/EZQWTCM954mrsUlp3v/1gHznssCp/FqYSOKbUrgLftnePtKjqey94ZuedrvR47X+TvIom/1h3i6ubx32//kdvhSAbe8M1dJ70tzRYhb5sxpJ+j1s8G7Mrb0oxpbO3tjWc/TnbCO1v4fiG3tyXmA/rwsjh1dfP49evXhx3ODDOlagPeFDDoSYG6mMF0jGmpWa/ZnDpvSKc53vTP1AtOkXUVg816o/0BdeH+YB3cHIBZuxfAjJthfH/AFDAY6udwn8x4s6hcXqY0sR3R7a15+YvjN9vbYYd78uSDOgYsGj4vXqkMZjwjEoqpfZoiTJ+ZwnPHS+VQmc3gaF3elISO5w3XBYKZ4yhNHaaxZAqzP2S9xfSQbdKq6TPa/6b/9fTF8dlse3uzw/34Jz998uSDZUy9qnhxfCq62sx4RiQRE6rXlCr6RvbHHCRTh/rJiJh5cTl8vV7Y8ixTFwhmjqPMFdhPcxykMGeR9YJT/Q6BT9Af0C4Qb+p7RdU6TOHiaEQ/ewOX0zy13N4OO9ybz+HwamDE8anEaqungFyhLFEdIGueahae62czr4en2Zk3rZPI2wxmjqNEr2NzPBpJ1gtOhfSjPpvT+YsbWidM23uuu1iF6E9ORpd7sb3d3d09f/634nM4k/rsfB+c1/PcxMvbLDkki7MkkjabQB6DoqL9bF6LaN4eHSa+ORHoAP3DSLppwE9zPBoJzEdbwfeH9xntP8iLOxkt1hSsdcSmJP7dmzk4ObfBcnu7u7v79ttvv/nmm7/89as336UEK9jLMllLCq/g6PLCpYEwXtMswazFqwj3LT0O9Kd4P/kUZF1DdCZuncwVzbz1MdA3ATqMn2YWpj84wKzIs0qmTvtMj2Mzubz1RL5esQoDf2uJeSEP29thb/v6669fvXr1xRdPQ/9aANxL4rzR5T4iuu/ECTNqe/Pe2rz/gx/Ve9vLly9fvHjx2Wd/SOxwusfeKXTRj44ugThV1v6dk1c3j8297fb29vnz559++vvL64ejcgkhhBBv2WB7O+xtX3757M3ednn9cPnfqFxCCCHEWzbY3g5729WDH/7yV7++vb29vH54cfm9USmEEEKIkg22t8PednHx4PLm0dvvSd48GpVCCCGEKNlgezvsbdM0XV6/9+GHHz179uzy+j19ASeEEGIt1t7eLq8fHva2aZouLh5c3Tx6+vTpPH9yefP+qCxCCCHEPbb/a92X1+/N8yeff/65tjchhBBrsf32Nl1cXd08urz+/sXFzYpZhBBCvMscYXsTQggh1kbbmxBCiDNE25sQQogzRNubEEKIM0TbmxBCiDOE+Xtvy3j97nCG/l+yfnJ/iKTT5Hyffp20Qk9e7+Uo/bXrSlyC9Yyt+pcK5orhKcYyyuT+Kx1J8691120duBqiOidxbZb9afbKOxsSGUg6EWkSxBwUOusF0/vbiK/X0v/wS7b2MkgvOSZ47XXVqbzlLQZg7o5O/T2UuR3e9gau/ageRXVO4trgXtUPPq+oY917Wz5DwakeG0xL02Dxgs5cZOo1xEO5mOC0+fWqPtYt5rHB9d1DmUtWt5Te3mpnYNA75psO4kNXbhmJveFisT6THU/x/Ex+vbg5UR3POUgRyjtVeCWH6vV0cv4n65KZ/mt9UEtnXbz/Ot6ryyyK99n0A1J48d4sxg8/XhwvI5m6zInN+Fp/rhheb10RBtdbvwR5mynI+DCdX72BS4WP65dNmheMFCw8p/3zJnlL5kjP8RSstzlST2emeJaaJffXm0gaOlUnZaym6zJLY/rA+/E0e46ZEaaoUd6mRVuYvjV1zLk4vkgNKiWT4rzNFF4k9s/kPRp4e1tymBK6nEybSMz4RFuXFfX4500yME0GKZrXC5fJ+6l1PDUvL46v0+G6POdMRi9p8xQOBnNBQ8i6vGOvP3W6kJ/mRGym1sR9wwaYehkFZrxoi1evdwr0wdQHfkDSUB+aKepIU78ogYk/Gu/yV2+MTlQf14ivfSgv30xsO+pnri63p9NsFyhhmS4kAuY228j46Zw7pC5Ph6mX9NPUZ46Z0qJ9aM5lqo5G4rxMP9N5vbPRvpGzQmZCfkidwexheyMrDOVidLzBgfpmyUAk3U+mz6RJ7Ke254kw7TJtDKw3rY9P1TG45/OCsXWR8aZn5rjHs2eA9M83IeeH6U8zr3eqJ+8afWBSM/pMjV4WJu9gottbUVtdttdT88pFy8P6jA4INn12XrbElSvS8fWa40y92GTdh8lqS6HGd6COH1Jv7bM+y+uDKpr98QZH1bU8VRwAG4nUXrDnp05dTOf70KyX1OGLqs2TPnN569L4PkTrBVNIHfMlLu046LeWCGEy9kbdz22/Hyfngfq5X7S9CVEz6q3o3t7S7s3PqaN+7hptb0J46OElxAnjbG/TPN/7rx9PJ5qi009R11JqeMk7ARe1dr2n28+l7foYLx7Q6nrECw75BOKn2HwhBuBtb9PibhxyhwCR7be35vj5PRRG9X9s6j1j2sZ7XlONXGN8xxg/J9p/IXphtrdp5Ttky9uP2d7O8j2vtrco5I7FBNdfSDW/mE50TNubEPdIbG/gmzP1sRdfjDO335BvwjS3N14t1B9vfDlC9g37YfbvOj7dfyCy6vX1+kb6YRi1vQGf6dShWaHFjC9xPci7Xft+EaKk56s3vF5BMBPv0anjPf6W90xIqp7e9ANSJx6FwAyTlNRncoXWA6npxSxfMn3rXCfek9eLb9YFxEmH2E9CKrqezf4P1PdKiOYV7y6dX73xj49Rj1fvWZPAK4rUBLcreNKBcU8zVwsIyCUKXfeo/qi+MY9jkmY88ziu62IudG5Jd+qk1zOZaO37RYiS6PaWfpz1P15xcGKhm48hXq3Zn2Ywk4usa+3+R697jz6T15sS7UPCUjMgWkvntuTNioqE1vPa+un7RYj/E/3JyeayI+/5UdvbGo9p3lJdcuJxn3tMR+NPaHtbo289fkbp8/FYgbmOifvLnNJ/j9Qp1rhfCn0hpin1796Wg/wTx1vZpo5JU4dc1s26oneI2QSybyDjSnX197/2vwzA8T3+67xeMPbD9LNuWnO9YX1Sx0tanG3mxTpMybx+lJXul2KKEOirN8Gg20mcB9usZN0vYju0veXoefcqxN5Yez3rfhFHQNubEEKIM6T5oyWd33Y35w4k8ZaQ+eykFgQpQgZM5dwbW/xxCzk9+hmSEEKcBvhHSw7Un/FO3GO9/nx4+EMzuruY/vE2DFKEivL2kmaWZt7o2wimD9rhhBCny38BboJ8GWulW7wAAAAASUVORK5CYII=" />

Here we have written a really simple "Employee" class. We then instantiated it from within the Main method, by creating an object called "Dave". After the object has been created, we then populated it with data.

However the above approach isn't best practice mainly because the fields' access modifiers was set to "public". This breaks one of the 5 pillars of OOP, called encapsulation, which requires that access to a class's field must be restricted so that only methods within the class can get/set the field's value.

If we simply replace "public" with "private", then the the program will fail with the following errors:

<img alt="" src="data:image/png;base64,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" />

"private" means that only methods within the class itself can read/modify (aka get/set) the field. We are allowed to make these methods public, so that we can call these methods from outside the class....which is what we do in the next approach.
<h3>2nd Approach : Access/Modify (private) fields via the use of (public) methods.</h3>
Writing the code like above isn't best practice. That's because a class should be encapsulated. hence a class's internal fields should not be readable/writeable directly. In other words the fields need to be set to "private", and we access/modify these fields using methods.

To make this possible, what we could do is write a method for setting a field's value, and another method for getting a field's value. That means we will end up creating 2 methods per field, and since we have 3 fields in the Employee class, it means   that we will have to write (3*2) 6 methods altogether.

i.e:

[csharp]

using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

class Employee
{
    private int age;
    private string firstname;
    private double salary;

    public string GetFirstname()
    {
        return firstname;
    }

    public int GetAge()
    {
        return age;
    }

    public double GetSalary()
    {
        return salary;
    }

    public void SetFirstname(string ProvidedName)
    {
        firstname = ProvidedName;
    }

    public void SetAge(int ProvidedAge)
    {
        age = ProvidedAge;
    }

    public void SetSalary(double ProvidedSalary)
    {
        salary = ProvidedSalary;
    }
}

class Program
{
    static void Main(string[] args)
    {
        Employee Dave = new Employee();

        Dave.SetAge(50);
        Dave.SetFirstname(&quot;David&quot;);
        Dave.SetSalary(35461.32);

        Console.WriteLine(&quot;{0} is {1} years old and earns  £{2} per year.&quot;, Dave.GetFirstname(), Dave.GetAge(), Dave.GetSalary());
    }
}

[/csharp]

This again will output:

<img alt="" src="data:image/png;base64,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" />

As you can see, we have added a lot more code but the output is still the same. However this second approach, while better, isn't best practice either. That's because:
<ul>
	<li>A method is designed to do a lot more then setting/getting a variable info. Creating methods solely for setting/getting fields values  overkill.</li>
	<li>This approach has a lot more of an overhead in terms of lines of code.</li>
	<li>Setting/Getting fields via methods, means that the code has become a little harder to read. For example we now have to write "Dave.age(20)" which is harder to follow. It would have been better we could just do "Dave.age=20;"</li>
</ul>
That is why there is an even better approach....which is to use "properties". I'll cover this in the next lesson.

&nbsp;