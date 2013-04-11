---
layout: post
title: "Faster Controller Tests"
date: 2013-04-01 14:20
comments: true
categories: [testing, rails]
---


A big issue with doing TDD is having fast test, or else running your tests will slow down your development cycle. One thing I always try to do is hit the database as less as possible, since what your testing is almost never directly related to the database itself.

This is easy enough to do by stubbing out the calls to model or `ActiveRecord` code. On a real application though, you'll usually have to authenticate users and/or do some kind of authorization. This too should not have to go through the database.

For authentication I'm using Devise and for authorization CanCan, and setting up a controller test on RSpec goes somewhat like this:

``` ruby support file
def controller_full_setup
  setup_devise
  setup_ability
  setup_host
end

def setup_ability
  @ability = Object.new
  @ability.extend(CanCan::Ability)
  @controller.stub(:current_ability).and_return(@ability)
end

def setup_host
  @request.host = 'test.example.com'
  default_url_options[:host] = @request.host
end

def setup_devise
  @request.env["devise.mapping"] = Devise.mappings[:user]
end
```

``` ruby spec file
it "does something" do
  controller_full_setup
  @ability.can :do, :action

  # rest of the test
end
```

With this setup you are logged in to devise as a user, and have a blank cancan ability object in which you can define the abilities needed for the purpose of the test.
