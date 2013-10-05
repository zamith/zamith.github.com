---
layout: post
title: "A good commit message"
date: 2013-05-18 18:05
comments: true
categories: [git, productivity]
---

Lately I came to realise that one of the most important things in a team's
development process is good documentation of what everyone's been doing.

If you know me well enough you'll know that I hate documentation, which seems
rather contradictory, but I think it all boils down to what good documentation
means.

Good documentation, in my opinion, are not wikis, a bunch of shared documents,
progress reports, etc... That's management crap. All a development team needs is
a commit history with good commit messages.

Note: I'm using git and git nomenclature as an example, but this applies to any
versioning system.

## Commit Message Example

![commit-msg](/images/commit_message.png)

Here's an example of what I consider a good enough commit message. It is
composed of three parts:

1. A first line of no more than 80 characters that sums up the purpose of the
   commit
2. One or two paragraphs to explain the changes from a user point of view (user
   story if available)
3. A bullet list of technical modifications of note

## Integration into the flow

If you follow a flow that's similar to
[mine](http://blog.zamith.pt/blog/2013/01/30/developing-quality-code/), you
don't need to worry about writing this messages in every commit, since that
would be tedious.

What I do is, when on a topic/feature branch I commit at will, with any message
I see fit at the moment. This well written message comes only when creating a
pull-request, so that someone doing a code review can better understand the
changes.

## Conclusion

This way of doing commits, in my opinion, make it really nice to read through
the git log of summaries, and when in doubt of what a commit does, it's easy to
get more info, providing a nice way of developing as a team. Although it's also
a reminder of why something was done that way, for when you're looking at the
code some time later.

