---
layout: post
title: "Developing quality code"
date: 2013-01-30 23:45
comments: true
categories: [development, rails]
---

Every developer has its own way of doing things and a set of programs he likes to use when developing, and having your work station tuned to get the most out of you is very important. Yet, when working in teams some ground rules must be set so that everyone is on the same page and that everything goes as smooth as possible.

There are enough bugs in an application, we don't need more in the development process.

These rules can go from which version control software to use to code indentation and method naming, it is entirely up to you and your team to define these criteria at the beggining of a project. You can even go as far as writing them down so no one forgets them, or if a new member enters the team he can quickly get up to speed, here's the [set of rules](https://github.com/groupbuddies/guides) we adopted at [Group Buddies](http://www.groupbuddies.com/).

It is my personal belief that the ultimate goal in a software project is to write quality and functional code, defining these rules is but a way to get off on the right foot. As anyone who has done more than school programming assignments knows, code in a big project tends to become cumbersome and to decay in quality, making the project hard to work on and more adverse to change. Let this go on long enough and you'll get to a point where you have only two options, rebuild from scratch or abandon the project.

That's why great code is not just a plus, its a must have!

By now you're probably thinking this is going to be yet another rambling about how awesome TDD or BDD is, well, it is not. TDD is definitely awesome, but nowadays I, as a developer, take it as part of coding. If you're writing code and not testing it yourself you're doing it wrong, at least in a web development project (and almost any other, I'd risk say).

Confused? No need for that, what I'm going to write about is a development flow that focus on improving code quality. It can be divided in 5 major steps:

![code](/images/code_process.jpg)

1. Develop features in separate branches
2. Code
3. Run quality tests a.k.a. check for code smells
4. Peer code review
5. Wrap up

By now some of you might be shaking your heads in complete disapproval of what I just proposed, that's fine, this is just the way we found to best serve our goal of improving code quality, you're more than welcome to do it your own way and to share it with others as I'm doing, if you so please.

If you've read until here though, I'm assuming you agree with at least 80% of what I'm saying. Let's try and get you to 100%.

### 1. Develop features in separate branches

This step may be a little bit git oriented, but that's what we use, you're more than welcome to adapt this to your version control system.

Whether you use [git workflow](https://github.com/diaspora/diaspora/wiki/Git-Workflow), [Github flow](http://scottchacon.com/2011/08/31/github-flow.html) or any other flavor of these, one thing is certain, each new feature should have its own branch with a descriptive name (I like putting my initials at the beggining of the branch's name, so everyone knows I'm responsible for it).

This serves two purposes, the first is that master or/and development should not be developed on as it will lead to a big mess of a history log and make it really hard to rollback a commit. The second is that having your own branch to mess around with, gives you a lot more freedom and a lot less fear to commit often.

One thing of note though, do not forget to rebase from the development branch from time to time (I find it ok to rebase once a day, but it depends on how big your project and team are). Also, resist your urge to create a branch from a feature branch, ALWAYS branch out from the development branch, or else you might get yourself into a branching nightmare.

### 2. Code

No, I'm not going to teach how to code.

This is where you do your job as best as you can, but remember: Red - Green - Refactor.

### 3. Run quality tests a.k.a. check for code smells

This is probably the step the least people do on a regular basis or have ever done before, still I assure you your code will get better and you a better developer with it.

In order to attest the quality of code you can rely on some great gems to help and guide you to where a refactor is due. The first of these is the [flog](https://github.com/seattlerb/flog) gem, which uses [ABC metric](http://c2.com/cgi/wiki?AbcMetric) to assign each of your methods with a value which you should aim to keep low (between 10 and 15 should be a reasonable range).

On running these tests always remember that they are mere signs of a potential problem and a help on your quest for good code, if at any point it starts being an impediment, by all means stop doing it.

Other gems worth looking at are [reek](https://github.com/troessner/reek/wiki) and [flay](https://github.com/seattlerb/flay).

Recently we've been using [Code Climate](http://codeclimate.com) to test the quality of our code and I have only one word to describe it. AWESOME! You should definetly check it out.

Both these methods will only tell you where your code might have problems, not how to fix them. For that you might want to take a look at [Ruby Science](https://learn.thoughtbot.com/products/13) and [Clean Code](http://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882).


### 4. Peer code review

After your code passes your own code review, it is always good to give it to someone else to take a look. This is a great policy mainly for two reasons:

Firstly you might have let an error slip or the person doing the review might have a different opinion on how to implement something. Secondly if you have code that is hard to understand that person will probably pick it up, and that is something your future self will appreciate when he reads the code again.

We do peer code reviews through Github's pull requests which allows for a discussion to happen on the same place where the code is. Then the code should be reviewed by a certain number of people (quantitative) or by someone in particular (qualitative), it's up to you. We approve a code review as soon as anyone from the team gives a thumbs up.


### 5. Wrap up

At this point you should have an accepted pull request with pretty good code, now just rebase your branch with the development branch, merge development with your branch and delete your branch.

And we're back at the beggining, ready to do this all over again for a new feature.
