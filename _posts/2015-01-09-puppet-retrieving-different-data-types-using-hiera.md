---
ID: 262
post_title: 'Puppet &#8211; Retrieving different data types using Hiera'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-retrieving-different-data-types-using-hiera
published: true
post_date: 2015-01-09 00:00:00
---
Hiera is a "data lookup" command line tool. This means that you tell Hiera what piece of data you are interested in, and it will retrieve that data for you by looking through one or more yaml files. It can also look up json files, but we'll stick with yaml files, as our mark-up language of choice.

Earlier we saw how to lookup data from a really basic yaml file. However the yaml standard allows for more sophisticated yaml files, yet still remain quite human readable.

Yaml files can hold data in various types, the types we are interested in are:
<ul>
 	<li><a href="http://www.yaml.org/YAML_for_ruby.html#simple_mapping">Simple mapping</a> - In the world of yaml, the word "mapping" means linking something to a key. This "something" can be anything, e.g. a string, or a array. However a "simple mapping" specifically refers to mapping a string to a key. Hence this is how represent a simple string variable in yaml form.</li>
 	<li><a href="www.yaml.org/YAML_for_ruby.html#simple_mapping">Mapped Sequence</a> - In the world of yaml, a "sequence" is a another word for array. So in this case we have a variable that's storing an array. You can also write this in a single line using the <a href="http://www.yaml.org/YAML_for_ruby.html#simple_inline_array">simple inline array</a> syntax</li>
 	<li><a href="http://www.yaml.org/YAML_for_ruby.html#nested_mappings">Nested Mappings</a> - The word "nested" here refers to key that holding one or more other simple mappings. I.e. This is how you create a hash table. You can also write this in a single line by using the <a href="http://www.yaml.org/YAML_for_ruby.html#simple_inline_hash">simple inline hash</a> syntax</li>
 	<li><a href="http://www.yaml.org/YAML_for_ruby.html#boolean">Boolean</a> - these are essentially simple mappings that are storing special reserved strings, which are any of the following: true/false, on/off, yes/no.</li>
 	<li><a href="http://www.yaml.org/YAML_for_ruby.html#integers">Integers</a> and <a href="http://www.yaml.org/YAML_for_ruby.html#floats">floats</a> - These are stored using the same syntax as creating a string variable, aka simple mapping</li>
</ul>
Now, in yaml, you can <a href="http://www.yaml.org/YAML_for_ruby.html#simple_sequence">declare</a> <a href="http://www.yaml.org/YAML_for_ruby.html#nested_sequences">data</a> <a href="http://www.yaml.org/YAML_for_ruby.html#mixed_sequences">without</a> a key, however in the context of puppet, these are meaningless and hence are not used.

Looking up data using hiera on the command line is actually quite limited, because you can only retrieve whole data items, and not a particular part, e.g you can't pick out a particular item in an array.

Hence querying data using the hiera command is mainly used for <a title="Puppet - Hiera troubleshooting" href="http://codingbee.net/tutorials/puppet/puppet-hiera-troubleshooting/">hiera troubleshooting</a>.

Let's say that our hiera.yaml file points to the following yaml file, called global.yaml:
<pre>[root@puppetmaster yaml]# pwd
/etc/hieradata/yaml
[root@puppetmaster yaml]# cat global.yaml
---
# The first line of a yaml file always starts with 3 hyphens, as shown above.

# Here's a simple mappingi, aka a simple string variable. This is analogous to
# creating a variable called "dad" which houses a value of "homer"
dad: homer


# Here, the "::" doesn't have a special meaning. It is simply treated as part of the
# variable's name, i.e. in this case the variable's name is "family_member::son" and
# it holds the value "bart"
family_member::son: bart


# Here's a mapped sequence, aka an array. This is analogous to creating a
# an array called "fruits" which contain 3 array items.
fruits:
 - apple
 - banana
 - carrot

# Here's a slightly differently way to write a mapped sequence.
vegetables: - potatoe
            - carrot
            - spinage

# Here's an example of a nested mapping, aka a hashtable.
stuff:
 fruit: apple
 name: steve
 sport: baseball

[root@puppetmaster yaml]#
</pre>
Now here's how you lookup and retrieve data from the yaml file using hiera on it's own:
<pre>[root@puppetmaster yaml]# hiera dad
homer
[root@puppetmaster yaml]# hiera family_member::son
bart
[root@puppetmaster yaml]# hiera fruits
["apple", "banana", "carrot"]
[root@puppetmaster yaml]# hiera stuff
{"fruit"=&gt;"apple", "sport"=&gt;"baseball", "name"=&gt;"steve"}
[root@puppetmaster yaml]#
</pre>
Note: the output for array and hashtable is actually in the ruby syntax. This means you can make use of these data in ruby code, e.g. in templates. Which we'll cover later.

See also:
https://docs.puppetlabs.com/hiera/1/data_sources.html