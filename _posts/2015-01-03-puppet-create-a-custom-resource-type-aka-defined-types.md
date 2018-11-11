---
ID: 258
post_title: 'Puppet &#8211; Create a custom resource type (aka Defined Types)'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/puppet/puppet-create-a-custom-resource-type-aka-defined-types
published: true
post_date: 2015-01-03 00:00:00
---
Let's say you have serveral wordpress servers, and each server needs to a different set of plugins installed on them.

In this case, you may want to create a class called "required_plugin" which requires a single parameter called "$plugin" and then declare this class multiple times, each time with a different parameter value (aka plugin name). This actually isn't possible with puppet, since you can only declare a class once within each node definition.

However you can achieve this with something called a "defined type" instead. You can declare a defined type multiple times within the same node definition.

Each defined type may ensure a collection of resource, however one thing you need to ensure is that the names of each resource (aka namevar) is unique. Otherwise we will have resources with the same names which will cause errors (even though resource attributes are different). One way to achieve this is to ensure that the resource's names are somehow derived from the input parameters, in order to make them unique.

The syntax for a defined type is nearly the same as a class's syntax.

https://docs.puppetlabs.com/puppet/latest/reference/lang_defined_types.html

https://docs.puppetlabs.com/learning/definedtypes.html

&nbsp;

http://grokbase.com/t/gg/puppet-users/13b6xec1bp/require-a-defined-type-from-another-module

&nbsp;

&nbsp;