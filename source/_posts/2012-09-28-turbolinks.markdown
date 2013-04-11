---
layout: post
title: "Turbolinks"
date: 2012-09-28 13:30
comments: true
categories: [rails]
---

I have been under a heavy load of work and not having much time to post as frequently and as awesomly (not sure this exists...) as I would want, but just found a gem that is so awesome I needed to share with you guys.

### Getting to the point

[Turbolinks](https://github.com/rails/turbolinks) is a gem mostly developed by DHH that makes following links in your web application faster without, and this is the best part, you having to change any server side code. It's like pjax on steroids!

Basically it works on the same premise PJAX does, which is that loading all your JS and CSS files everytime you follow a link is expensive (even if it hits cache), so it will just load the title in the head and the body.

Steve Klabnik has even made a [benchmarker](https://github.com/steveklabnik/turbolinks_test) so you can get the exact amount of speed improvement your app will get. He has also [posted the results](http://blog.steveklabnik.com/posts/2012-09-27-seriously--numbers--use-them-) for Basecamp Next, so you can check that out.

### TL;DR

Get [Turbolinks](https://github.com/rails/turbolinks).