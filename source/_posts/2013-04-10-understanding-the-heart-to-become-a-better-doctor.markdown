---
layout: post
title: "Understanding the heart to become a better doctor"
date: 2013-04-10 00:55
comments: true
published: false
categories: [testing, rails]
---

Yes, you read the title correctly, this is indeed a post about better
understanding the heart in order to become a better doctor. Of course that by
heart I mean testing and by doctor I mean developer.

The analogy is not that absurd, as matter of fact it is not absurd at all in my
opinion, as tests are the core of any application, especially one for the web.
Without tests an application won't live long, it will probably be able to stay
alive for a while, but at the first need for change or growth, it will fester
and die. As a developer I much prefer not being around when such things happen,
and most of all I believe it is my duty to impede this from happening, it's the
developer's code of honour, it's the reason I call myself a professional
developer.

As you can see I do have a very strong opinion on this subject, and I really am
suggesting that if your writing a web application without tests (preferably TDD
but not necessarily) you're not only doing it wrong, but you're being
unprofessional and utterly irresponsible. Plainly put, you should not be writing
code!

A little side note here to all of those starting in this business or that just
now have been introduced to testing, this article should be a good starting
point on your quest to a brighter future where tests lead the way. Moreover, you
should not include yourself in the group of irresponsible developers out there,
if you actually start testing, obviously. Testing is something that is rarely
introduced in CS majors and most starting developers don't get to have an older
and more experienced mentor to teach them this stuff, so it's really not your
fault and you have all my respect and appreciation for trying to learn this.
Also, not everyone has to be a professional developer, and that's fine. Just
don't try to impersonate one, that's all I'm asking.

Now that we're done with the contextualization let's get to the juicy part of
the post, where I'll introduce some basic concepts and keywords of testing (some
of which you might have already heard of) and slowly progress into more advanced
and "philosophical" ones.

## The religions

TDD, BDD and traditional testing. This are the most adopted religions in the
testing community.

You'll often see them referred to as methodologies, processes or even
philosophies, but I believe they're more than that. As a developer this will
drive not only your code but also the way you think and solve problems. It's a
way of life, so choose carefully and embrace it (well, maybe I'm being a bit
dramatic here, you can change your mind afterwards, trying to mix them is ill
advised, though).

### TDD

Test driven development, as the name states, is when you let your tests drive
your application's code. It is often coupled with an agile software development
process, as it too work in very short cycles composed of three phases:

1. <span class="red">Red</span> - You make a test for a feature you wish you had (usually it will test a
   very specific part of that feature)
2. <span class="green">Green</span> - You write only enough code to make the test pass, not more, not less
   (at this point you should commit your work)
3. <span class="refactor">Refactor</span> - You are now in a green field, where you have a solution that may
   not be optimal but it works and a test to make sure that it ever stops
   working you'll know about it. This is the perfect time to think of a better
   way of solving the problem

This is the core of TDD, it states that if you repeat this cycle over and over
again you'll have smaller and more focused classes, looser
[coupling](http://en.wikipedia.org/wiki/Coupling_(computer_programming)) and
cleaner interfaces.

There is, AFAIK, no real proof of this, but TDD does provide you a great test
suite, which combined with a control version system gives you much more
confidence in your code, as well as more freedom to change things and  an easier
way to debug regressions.

TDD can actually be divided into two subgroups, according to Martin Fowler, the
classical and the mockists, but we'll get to that later on.

Right about now you should be wondering, "if TDD is so great, why even bother
having other ways of testing?". It turns out that TDD is not perfect, mostly due
to our own faults as developers, such as the tests failing to cover an important
edge case or the tests being badly written leaving us a bigger code base to
maintain. Another problem might be that we as overzealous as we are, try to test
every possible scenario for every little bit of code, this is counter productive
and to know what and how much to test is a kind of art that will come with
experience (we'll cover some ground rules that make this an easier task,
though).

When doing TDD (or any kind of tests) there is one thing you should be
constantly aware of, "your code is NOT bug free!". Rest assured, bugs will
appear, but they will probably not be on your main flow of things, and when
they're fixed, they'll stay fixed.

Uncle Bob Martin describes TDD in [three basic rules/laws](http://butunclebob.com/ArticleS.UncleBob.TheThreeRulesOfTdd):

1. You are not allowed to write any production code unless it is to make a
   failing unit test pass.
2. You are not allowed to write any more of a unit test than is sufficient to
   fail; and compilation failures are failures.
3. You are not allowed to write any more production code than is sufficient to
   pass the one failing unit test.

One of, if not the most important thing in TDD is that you write your own tests.
I like to go a bit further and say that the tests are part of the code.

## BDD

Behaviour Driven Development is largely based on TDD, it differs mainly in the
fact that it focus in the behaviour of things instead of how they work, which is
why BDD always starts by defining a scenario. Plainly put, you focus primarily
on the user interaction and the user's point of view.

For instance, imagine you would like to implement and ATM, with TDD you would
probably start by testing if the withdrawal and card validation mechanisms,
whereas with BDD you would write a scenario such as this:

``` cucumber Scenario 1: Account is in credit
Given the account is in credit
And the card is valid
And the dispenser contains cash
When the customer requests cash
Then ensure the account is debited
And ensure cash is dispensed
And ensure the card is returned
```
Almost everything else in TDD will also apply here, but this small change in
perspective will have a deep impact in the resulting solution.

This type of scenarios have some rules, though, they have a special syntax so
that they can be understood by the non-technical people of the team or company,
but also so that they can be automated. As a matter of fact, doing this you have
not only a test suite, but also a pretty good documentation and feature
specification.

If you ever tried TDD you probably have faced the "what do I test first"
situation, BDD sets to solve that as you always start with the scenario and let
it drive you. In the example above you would first write a failing test that
would check that the account has credit, and then make pass, then you would
write a failing test for the card validation and make it pass, and so on. Bare
in mind though that making these tests pass may require going to different
layers of the application and further testing, but you'll always know what to
test first.

BDD is more elegantly described by it's creator Dan North from whom I took the
ATM example in [this post](http://dannorth.net/introducing-bdd/).

## Traditional Testing

I call this traditional testing for the lack of a better term, but this is the
model you'll see in companies that adopt very strict development methodologies
such as [waterfall](http://en.wikipedia.org/wiki/Waterfall_model) or
[RUP](http://en.wikipedia.org/wiki/IBM_Rational_Unified_Process), and it happens
when there is an entire team whose only purpose in life is to do write tests.

In this model the tests start to be written after the development is complete,
IMHO it some serious drawbacks (even though I've never personally used it, I
must admit):

1. The tests and it's expectations don't drive the code and the probability of
   having poorly written, extremely coupled code is much higher
2. Management is always pushing for deliverables and/or deploys, in the event of
   delays the test phase will be the first to suffer the consequences

Even if the test process starts in sync with development process it is harder
and less productive to have someone else write the tests for your code as you're
writing it.

In case you haven't yet figured out, I'm nor a big fan, nor a big connoisseur of
this type of testing, still I did not want to go by without mentioning the ways
of the past.

## Going a bit further into TDD

The main focus of the rest of this post is going to be TDD with Rails, but as
I've said earlier TDD has two subgroups, from which I'll choose one (not 100%,
but almost) and even borrow a few high level concepts from BDD.

### Classical TDD

When writing tests, classical TDDers will not use mocks and use stubs as little
as possible, mostly to fake web services and the such. They focus entirely on
the final state of the system, in the ATM example, they would test that the machine
has given money to the user, for example.

In this approach the tests for an object will test it's functionality as well
as it's neighbours'.

### Mockist TDD

Mockist TDDers will use mocks to filters everything that is not the SUT (System
Under Test). Taking the ATM example one more time and imagining the SUT is the
card validator, they would test that if the card is valid, the
`give_money_to_user` method was called on the money dispenser. The focus here is
on behaviour instead of state.

In this approach the tests for an object will test it's functionality as well
as it's communication with it's neighbours.

Check out Martin Fowler's
[article](http://martinfowler.com/articles/mocksArentStubs.html) on this for a
much more in depth analysis of the difference between classical and mockists
TDDers, as well as the difference between mocks and stubs.

## Outside-in testing


