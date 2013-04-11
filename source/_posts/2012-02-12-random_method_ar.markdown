---
date: 2012-02-13
title: Adding a method to all records
layout: post
categories: [rails, active record]
comments: true
---
If there are some methods you wish all of your Active Record instances would have, the easiest way to add them is to [monkey patch][mp]. Here is how to add a random method that retrieves a random record from a table:

{% highlight ruby %}
module ActiveRecordBaseExtension
  def random
    self.find :first, 
              :offset => (self.count * rand).to_i
  end
end
ActiveRecord::Base.extend ActiveRecordBaseExtension
{% endhighlight %}

This code can be appended to `lib/rails_extension.rb` which should be required in the `config/environment.rb` file:

{% highlight ruby %}
require 'lib/rails_extension.rb'
{% endhighlight %}

[mp]: http://en.wikipedia.org/wiki/Monkey_patch