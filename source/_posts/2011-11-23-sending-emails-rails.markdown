---
date: 2011-11-23
title: Creating emails with Rails
layout: post
category: rails
comments: true
---
By default, rails sends emails with the MIME type *text/plain*, but sometimes you might wish to make your emails look better, by adding links or images and for that you need your mail to have the MIME type of *text/html*.

The easier way to do this, would be by change the content type variable in the `notifier.rb` file, but if you do that, email reader that don't support html will not show your email correctly.

So how have the best of both worlds with as little effort as possible? Fear not my friends, for RoR convention over configuration policy has come to the rescue yet again!

All that you need to do is to create two files in your notifier views, one with the name `whatever_you_want.text.html.erb` and another with the name `whatever_you_want.text.plain.erb` and rails ActiveMailer will take care of the rest for you. Sweet!

*PS: This is for rails 2.x, in rails 3.x the names would be `whatever_you_want.text.erb` and `whatever_you_want.html.erb`*