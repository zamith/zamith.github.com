---
layout: post
title: "Improve your engines with Mechanic"
date: 2012-10-14 02:38
comments: true
categories: [rails, engines]
---

The first time I created a Rails Engine, there were some parts of it that were confusing as it was just like a Rails App, but no quite, just like a gem, but not quite. There was a lot of digging through old or non existing documentation about how to configure an engine I've just created.

Nowadays with the rising popularity of engines, the documentation is getting better, still there are always some little things I have to do every time I create a new engine. Since this is boring work, I decided to create a gem to do this boilerplate work for me. And thus, [Mechanic](http://github.com/zamith/mechanic) was born!

This idea is largely based on thoughtbot's [Suspenders](http://github.com/thoughtbot/suspenders) that tries to do the same thing for normal apps. Mechanic is very new and still a diamond in a rough, so if you spot a bug or have a feature you would like added please let me now in Mechanic [github issues page](https://github.com/zamith/mechanic/issues).