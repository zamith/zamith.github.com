---
layout: post
title: "Testing JS with RSpec features"
date: 2014-02-11 17:20
comments: true
categories: [testing, rails]
---

I've lately moved from Cucumber to [RSpec features](http://robots.thoughtbot.com/rspec-integration-tests-with-capybara),
and have found the need to test some features that rely on javascript in order to function.

## The Basics

This is very easy to do with [capybara-webkit](https://github.com/thoughtbot/capybara-webkit), you just need to add one line to your `spec_helper`:

``` ruby
Capybara.javascript_driver = :webkit
```

And a little magic flag on your RSpec example:

``` ruby
it "does something", js: true do
  ...
end
```

However, I have found myself in situations where, for a specific test, I want to
use the selenium driver. The reasons can go from capybara-webkit not behaving
like the real browser to, more commonly, me being on a debug mission and wanting
to see it rendered on the browser.

## Dynamic driver

To solve this problem I came up a solution that allows me to dynamically choose
the driver I want for a given example.

I just added this snippet of code to my `spec_helper`:

``` ruby
RSpec.configure do |config|
  config.before(:each) do
    Capybara.current_driver = select_driver
  end
end

def select_driver
  if example.metadata[:js]
    if example.metadata[:js] == :selenium
      :selenium
    else
      :webkit
    end
  else
    Capybara.default_driver
  end
end
```

This allows me to use the default syntax:

``` ruby
it "does something", js: true do
  ...
end
```

or to choose selenium:

``` ruby
it "does something", js: :selenium do
  ...
end
```
