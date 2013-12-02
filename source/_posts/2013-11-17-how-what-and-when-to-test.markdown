---
layout: post
title: "How, what and when to test"
date: 2013-11-17 19:59
comments: true
categories: [testing, rant]
---

Testing is hard.

It's not that it's hard to write tests, but they're hard to get right. It all
starts with a little bit of tight coupling and a mock or two in the wrong place,
but it quickly scales to a huge and brittle test suite.

At this point your tests fail with every little change to the code base, they
take forever to run and the whole development team feels bad about them, blaming
them for every problem.

The first time you try to add TDD to your team's process it is very easy to dig
yourself into this kind of hole, where your test suite adds no confidence,
design feedback nor team velocity. This will usually happen due to a lack of
understanding of the TDD philosophy and lack of experience writing tests.
Believe me, I've been there.

The worst thing you can do when facing such a situation is to blame it on TDD
and discard it completely. It is great and it definitely works, but if you have
no experience with it, you might want to ease your way into it.

So, let's break TDD down into three major goals

1. Confidence in your system
2. Design feedback
3. Team velocity

The first we'll try to get is confidence, then feedback and velocity will
hopefully take care of itself. In order to do that, I came up with this chart:

![test-quadrants](/images/test-quadrants.png)

The **top left quadrant** is the simplest to implement on a team, you do high level
acceptance or integration tests on the critical paths of your app, after the
features are written. The goal is that if any of these tests fails, you're app is
not usable. This is a good way to start adding tests to an existing code base.

The **bottom left quadrant** builds on top of the first, but adds confidence by
adding some unit tests to critical algorithms in your app. The confidence you gain
is that if a big feature fails, you have a finer grained knowledge of where the
error might come from.

On the **top right quadrant** we start writing tests first, which will (if done
correctly) provide bigger design feedback and ultimately generate an overall
better code base. However, this is the time it starts to get harder, so your team will
really have to be on board with this, or else it just won't work.

On the **bottom right quadrant** we are writing both acceptance/integration and
unit tests for the critical paths of our app before the code that makes them
pass (one test at the time, obviously), we are on a good way to having TDD at
it's finest.

After you've nailed all of these quadrants, it's time to go full blown TDD.

### Takeaways

One thing to keep in mind though is that there is **no silver bullet** and this is
**not** a religion.

TDD works great in a lot of situations, but it may not be fit for others, just
give it a fair chance and weight the pros and cons on each situation. If after
that you feel TDD is slowing you down, consider dropping it.
