---
layout: post
title: Shipping Features Responsibly
tagline: Slides from GDC 2019 Tools Tutorial Day
category : conferences
tags : [gdc]
---
{% include JB/setup %}

---

<h3><a href="{% production_url %}/assets/shipping-features.pdf">Here is the PDF of my deck from the talk!</a></h3>

---

### Agenda

"With great power comes great responsibility"
-Voltaire / Spider-Man / Stan Lee

* This talk gives high level best practices of releasing new features
* All features don't necessarily need to follow all guidelines
* Any feature needs some combination of these

---

### Me

* Gamedev Tools Engineer since 2003
* Infinity Ward, Kojima Productions, Insomniac
* Leading a tools team at IW
  * We are hiring, and having a party tonight!
  * _tinyurl.com/toolshappyhour2019_ for details
* History in revision control, branching, file formats
* Built asset editors, level editors, tools frameworks

^ But this talk isn't really about me it's mostly about...

---

### My Evil Twin

* Extensive experience in _breaking_ artists & designers
* I've halted entire studios' progress for hours
* I've degraded artists' productivity for days
* I've released tools that are:
  * undertested (buggy)
  * underdesigned (don't work in practice)
  * overdesigned (confusing/too many features)

^ No details b/c of Vault, talk to me later tonight

^ All this qualifies me to lecture you about how to not be like me, and waste people's time

---

![inline](/assets/shipping-features-pending-changelist.gif)

^ You may or may not have consciously chosen to be here

^ You have written some code, decided things should be different

^ Or maybe you are following up on a feature request opened by a user

---

![inline](/assets/shipping-features-stop-sign.jpg)

^ Stop right there, buddy.

^ You almost definitely aren't ready to pull the trigger.

^ I'm here to tell you all the things you need to do (to not be like me).

---

### 1 - Question Your Design

* Have you _actually_ done all the design work to ship this feature?
  * Remember: _impact_ usually surpasses _intent_
  * Take a step back and make _intent_ crystal clear

* What problem are you trying to solve?
  * Is this something that only bothers (or makes sense to) _you_?
  * Imagine giving an "elevator pitch" of explaining this design
  * Do you sound like a crazy person?
  * Is this a waste of time/effort?
  
^ Try your best to talk yourself out of making the change

^ It's perfectly ok to start a lot of changes and walk away!

^ When in doubt: put it on ice and move on to the next thing

---

### 2 - Estimate The Impact

* Have you considered how it will impact:
  * Every workflow permutation (use case)?
  * How do you know? Have you searched the code (GREP)?
  * How will offsite staff and outsource vendors work?
  * Downstream projects (teams offset in time)?
  * Fellow engineers (different from users)?

^ A good time to do some "Rubber Duck Debugging"-style explainer of the change

^ A preliminary code review to help make the impact knowable

^ Just looking to understand the scope of your change

---

### 3 - Perform And Document Your Testing

* The bigger the impact, the more testing you need

* Be greedy with automated tests
  * No cheating: actually wait for them to finish!

* Critical code paths should be stepped through in the debugger
  * Don't end up saying "How did this _ever work_?"

* Document testing you have done in change comment
  * Adds value to the code review process

^ Remind code reviewers that your code works by stating tests

^ They aren't strictly looking for bugs, evaluating your approach

^ If tests are documented reviewers may think of tests you are missing

---

### 4 - Measure And Document Your Performance

* Is it faster?  Is it slower?  By how much?

* What is the bottleneck?  Did you change it?

* Have you measured the impact on resources?
  * CPU: are you wasting CPU cycles needlessly?
  * Memory: what about going wider on many-core CPUs?

* Disk Space
  * Have you considered how disk caches will be evicted?
  * How will you know if eviction has a bug?

* Server Load
  * Do you have caching (or rate limiters) on your key API routes?

^ You should know and document all these things

^ Take credit for speed improvements, and justify perf hits

^ Demonstrate through documentation that you have thought it through

---

### 5 - Prepare For Failure

* What does the worst possible failure look like?
  * How (and when) will you know if this is happening?

* How easy is this change to roll back?
  * Are there costs that make roll back painful?
  * If so, what is the commensurate change in testing?
  * Should you launch this feature as a features toggle (setting)?

* Are you _actually_ prepared to roll back if it causes a problem?
  * What time is it _right now_?
  * When do you plan on leaving work?

* How big of a problem are you willing fixing w/o roll back?

^ The best practice for releasing a change is to roll back at first sight of defect

^ Engineers, however, are in the headspace of thinking through the technology

^ Roll back first trains your muscle memory to respect others' time more than your own

^ Train yourself to roll back at the first sign of trouble

---

### 6 - Update User Documentation

* Have you searched for documentation that needs updating?

* There may be many places where "documentation" exists...
  * Wiki
  * Code comments
  * Documents in revision control
  * Recent message threads about an issue

^ Documentation falls prey to broken windows effect, fight it

^ Pointing to relevant documentation when people ask should feel great!

---

### 7 - Send Good Notifications

* Now the feature is release, it's time to tell people!
  * Always TLDR in your emails
  * Provide details for technical stakeholders (maybe separately)
  * If the performance wins/costs are substantial, tell people
  * Include images, GIFs, videos: eye candy helps!

* Consider and advise about issues you may expect
  * Include some advice for fixing potential issues
  * Be on call to answer replies within a time window

^ This is tricky to get right

^ Good communication is easy to lose and hard to gain

^ You have to make it short and simple to have a shot at keeping people reading

---

### 8 - Do The Follow Up

* Contact users that are meant to benefit from the change
  * Did they actually pay attention to your notification?
  * Did they actually benefit as you intended?
  * Did they experience a bug, and forget to tell someone?

^ This is critical to you improving as a developer

^ Any missed communication at this stage means you need new procedures

^ Remember that different users receive communcation in different ways

^ You may need to reiterate the same information multiple ways

---

### 9 - Post Mortem Yourself

* How many times did you have to roll back?
  * How are roll backs trending?
  * What can you do to decrease it?

* Verify with people randomly:
  * Your TLDR was short enough
  * Your details are pertinent

^ I like to do this in the kitchen, just ask if people saw and understood my notes

---

Review:

1. Question Your Design
1. Estimate The Impact
1. Perform And Document Your Testing
1. Measure And Document Your Performance
1. Prepare For Failure
1. Update User Documentation
1. Send Rich Notifications
1. Do The Follow Up
1. Post Mortem Yourself

^ Now you are actually done shipping your feature. It's a _lot_ of work:

---
[.build-lists: false]

### Questions?

Twitter: @gorlak

Community:

* Twitter: @thetoolsmiths
* Website: thetoolsmiths.com
* Chat: thetoolsmiths.slack.com

Call of Duty Happy Hour: _tinyurl.com/toolshappyhour2019_

See you later tonight!