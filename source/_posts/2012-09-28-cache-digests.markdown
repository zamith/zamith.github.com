---
layout: post
title: "Cache Digests"
date: 2012-09-28 15:05
comments: true
categories: [rails, caching]
---

Well, it seems today is a great for me in terms of gem finding. I've been taking a look at rails 4.0 new features, which are mostly already available as gems such as [Turbolinks](http://blog.zamith.pt/blog/2012/09/28/turbolinks/), and another one really stole my attention.

It's called [cache digests](https://github.com/rails/cache_digests) and it makes it super easy to do [russian doll caching](http://37signals.com/svn/posts/3113-how-key-based-cache-expiration-works).

I'm assuming you're also going to need to use [memcached](http://memcached.org/) if you want your expired caches to be removed automatically, but other than that it just works.