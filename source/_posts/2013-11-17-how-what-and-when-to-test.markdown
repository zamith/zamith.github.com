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

** Chart Bonito **

After you've nailed all of these quadrants, it's time to go full blown TDD.

### Takeaways

One thing to keep in mind though is that there is **no silver bullet** and this is
**not** a religion.

TDD works great in a lot of situations, but it may not be fit for others, just
give it a fair chance and weight the pros and cons on each situation. If after
that you feel TDD is slowing you down, consider dropping it.
