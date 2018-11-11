---
ID: 483
post_title: 'Foreman &#8211; Render provisioning templates'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/uncategorized/foreman-render-provisioning-templates
published: true
post_date: 2015-11-12 00:00:00
---
#!/usr/bin/env ruby


require "erb"
require 'ostruct'


class Basicerb

  def initialize name
    @name = name
    @template = File.read('/root/index.erb')
  end

  def render
    @person = OpenStruct.new
    @person.name    = "John Smith"
    @person.age     = 70
    @person.pension = 300
    ERB.new("<h1>Hello ERB World!! </h1><h3><%= @person.name %></h3>").result( binding )
  end
end

test = Basicerb.new "wow"


puts test.render