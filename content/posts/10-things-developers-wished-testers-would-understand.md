---
title: "10 Things Developers Wished Testers Understood"
date: 2020-02-27T14:03:24-06:00
tags: [ 'testing' ]
draft: true
---

A co-worker linked an article in Slack recently: "5 Things QA testers wish programmers understood".  Of the 5, none had redeemable qualities, and the whole thing smacked of condescension, so I figured I'd write a counter-point post...

## We're Generally Proud of Our Work...
So when you come to us with a bug in something that we spent weeks trying to get right: forgive us if we're not excited to see you.  We go to great lengths to test that a feature is fully functional, and resilient.  We often spend more time re-working the 'simple solution' than writing it; usually because we discovered some issue with 'the easy way'.  Whether it's integrating with a straight-forward 3rd party library, or even a simple feature - it's rarely straight-forward, nor simple.

## Writing Software is Extremely Difficult...
If we trivialize that 'bug' you found where the color of the error message is three shades too light: it's because that is insignificant to the challenges we face every day.  While we understand that we didn't follow the requirements, it wasn't intentional, and is often the result of the requirements **changing every ten minutes**, or the requirements not being complete, or we just put a placeholder value in - and forgot to change it.  It's hard enough to write the software, but if the rules keep changing, or haven't been written yet - then the game becomes impossible to win.

## We'd Love to NOT Write Bugs...
This isn't an easter egg hunt we've carefully planned. When the QA department starts imagining new ways to change the process so that we can 'write fewer bugs' - we know it's not going to change anything.  So forgive us if we aren't excited when you propose any of the following 'process improvements':
  - Code freezes *X* days before a release
  - "Freezing" requirements
  - Breaking things down into smaller tasks
  - Logging bugs in the backlog
  - "Grooming" bugs from the backlog
  - Tracking/Reporting how many bugs you found

## All Those Big Words Mean Something...
Terms like 'string literal', 'API call', and 'hexadecimal' have deep meanings in software development - that you should probably understand.  If only to prevent developers from having to talk to you like you're 5; because *nobody* wants that.  It's a waste of our time, it's frustrating, and you'll feel insulted at the same time.  Perhaps if you're going to work in the industry you should learn the language; and no - those words aren't *jargon*.

## We Understand That You Found Something Wrong...
But we have deadlines to meet, so I'm sorry if that error message color is a little off, but I've got to finish writing this new feature before the end of the sprint.

## You're There to Enable, not Prevent...
If bugs aren't going to prevent the user from using the software - don't report it as a "HIGH BUG" and stop the release.  It's not, it's the color of an error message, put it on the backlog and we'll get to it later.

## We'd LOVE to Get Rid of Old Code...
But it's there for a reason: it supports some feature that product hasn't wanted to re-write in the 'new code'.  If you want us to 'keep the code up to date' talk to the product manager about refactoring, or re-writing old features - see what kind of response you get.

## Testing Metrics Are Insulting...
There are developer metrics - like Lines Of Code (LOC), defects per LOC, velocity, bug-turn-time, etc... and they're all useless.  So when you're reporting that you found 17 bugs in Tom's code, and 9 in Jerry's - no one is happy.  Those numbers just turn into a weapon for PHBs: and you're arming them against us - please stop.  When you categorize your bug as a "Coder Error" before the guy who writes it can look at it, that's insulting.  If you understood code well enough to determine that a defect was an actual *failure* by the developer - you'd *be a developer*.

## 
## 
## 