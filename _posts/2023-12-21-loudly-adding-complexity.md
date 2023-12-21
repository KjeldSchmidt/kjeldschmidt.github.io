---
title: "Loudly adding complexity: A lesson on leadership"
date: 2023-12-21 00:00:00 +0000
categories: [Software Development]
tags: [software development, leadership]
---

I just had a split-second thought that something done by a fellow developer in a
team was "the exact opposite of leadership", based on them changing an
established pattern[^config-not-code], and doing so quietly, without asking the
team before or telling the team after.

I wanted to explore that thought - was it just a fleeting moment of annoyance or
was there some deeper insight in there? With the word "leadership" lodged in my
brain, I thought about a mentor and former tech lead of mine, and I remembered
some of the things _he_ did without consulting the team.

- He deleted manual approval gates for deploying to our staging environments
- He deleted some alerts from our monitoring system
- He deleted many cards and stories from the backlog

All that without telling anyone. So why didn't it annoy me back when he did
sneaky stuff, while I am bothered now? On reflection, the answer is rather
obvious.

He saw issues with the status quo and quietly removed things that he suspected
were painful obstacles and unnecessary overhead, but which the team might have
defended due to status quo bias, if he had consulted us first. So he did it
quietly and waited to see if anyone would notice or complain.

That is to say, he _quietly reduced_ complexity[^reversible].

But when he _added_ complexity, he shouted from the rooftops for all to hear.
Finally adding `tfsec` to our pipelines was a massively important late addition
to our process, and while he did the setup without announcement, he turned to
the team the moment it was ready and told us: "We can do this _now_. I've paid
the up-front cost myself, and now we can decide as a team if we want to pay the
ongoing maintenance cost. Please try it out and give feedback."

In other words, he _loudly added_ complexity.

But my teammate _quietly added_ complexity, by creating a scenario that will
inevitably lead to questions - "why do we do the same thing in two different
ways? what's the pattern here? who do I have to ask? is our config secretly
borked due to conflicts in the approaches?" - all while not leaving a morsel of
documentation or communication behind which would allow other team members to
find answers, other than to use the poorly named `git blame` on the unusual
piece of code.

And that's the lesson: a good leader shouldn't quietly increase complexity. If
you do something in secret, you better be confident that it _reduces_ mental
overhead for everyone, rather than increasing it. And if you _increase_
complexity, you better be loud about it.

[^config-not-code]: A configuration thing, which all of our repository handle in a single file in the root directory. The dev in question made some config changes inline instead, even though they knew the established pattern.

[^reversible]: And all of these changes were easily reversible if any of us _had_ complained.
