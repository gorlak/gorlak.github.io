---
layout: post
title: Tools Engineer Recruiting FAQ
tagline: Recruiting at IW
category : recruiting
tags : [iw]
---
{% include JB/setup %}

I recently wrote a document to help talent through our recruiting pipeline at Infinity Ward. It's come up in [the slack](https://thetoolsmiths.org) to positive reviews, so I'll repost it here. I wrote this because I felt I could pre-answer many questions about how the team is setup within the studio, and ease in talent from outside the game industry a bit better.

Before prepping this document I got a candidate or two that wasn't really clear on what the tools discipline is. Presenting a document that lays this out streamlined our company process and respects the applicatant's time better.

---

_Greetings!_

## Introduction

If you are reading this your resume/profile has been viewed and determined to be a possible overlap with the Tools team at Infinity Ward!  Tools work may or may not overlap with your background, or perhaps not even where your current interests lay, but this document has been written to clarify things enough so that you can decide if a role on the team is right for you.  Fit is very important to our team, so we put this document together to provide a better view of what Tools Engineering is at Infinity Ward.

### What is a Tools Engineer?

A Tools Engineer is a software engineer that supports the development of the content and code for the development of the game (instead of the game itself). They work to improve the rate-of-change at which the game can be built, to improve the quality of the game, and enhance the user experience of creating the game.  Engineers that gravitate toward tools development tend to want to:

* Analyze workflow and improve the usability of the tools that designers and artists use
* Improve the efficiency at which changes can be shown in the game
* Take measurements and communicate defects and inefficiencies in the game
* Support studio services that accelerate and supply information about the tools
* Improve the productivity of fellow engine and game engineers

---

## What are the different disciplines within Tools Engineering?

The Tools team at Infinity Ward doesn't mandate that people choose and only focus on one discipline.  Instead, it's meant to help identify which areas of tools development that team members find most gratifying.  There are frequent opportunities to do work in a discipline that might be outside your comfort zone.  Domain knowledge is deep within each discipline, so knowing/learning/changing which discipline is most interesting to a team member can help ensure that they stay happy and focused.

### Content Editing Workflow

Content editor work focuses on the traditional Windows desktop style application engineering.  This development is two-fold:

* Constant auditing and pursuing high quality and efficient user-experience of the content editing tools
* Improving the state of the underlying software architecture of those content editing tools

Skills and Concepts:

* User experience auditing and content creator communication/feedback
* User interface toolkits (such as Qt, wxWidgets, WPF, WinForms, FLTK, IMGUI, etc...)
* Document formats (such as XML, JSON, YAML, etc...)
* Revision control integration (such as Perforce, Plastic SCM, etc...)
* File system watchers and document hot-reloading
* Interprocess communication and shared object synchronization

### Content Build Pipeline

Content build pipeline work focuses on the asset pipeline and all its data processing technology.  The build pipeline is the collective noun for all of the tools that process data into the form that is loadable by the game (both the development version of the game as well as the final version that goes on the disc).

Skills and Concepts:

* Build systems (such as Make, MSBuild, Jam, FASTbuild, Ninja, etc...)
* Dependency analysis (modification time, checksum)
* Pipeline auditing for iteration throughput
* Parallelism and concurrency
* Global optimization
* Determinism

### Reliability & Infrastructure

Reliability work focuses on studio services that support all engineering, content creation, and the at-large process of facilitating change over the course of production.

Skills and Concepts:

* Continuous Integration (in its traditional definition: the folding of changes against each other for early detection of integration test failure)
* Continuous Validation (automatic validation as change commences within revision control)
* Computer Configuration-As-Code (such as Puppet, Ansible, etc...)
* Implementing and honing the reporting of the telemetry implementation within the studio
* Health monitoring and data corroboration of key services like caching servers (CIFS, Redis, memcachd, etc...) as well as distributed build systems (SN-DBS, IncrediBuild, etc...)

---

## What is the application process?

The process has three steps:

### Step 1: One hour phone interview with the team leader

Expect the call to take up to an hour, but it can be as short as 30 minutes.  A shorter call doesn't mean you necessarily did worse (or better).  The call consists of three parts:

* The short ice-breaker discussion of the team, studio, and broader corporation (mostly answering any questions you may have beyond the scope of this document).
* A technical "Skills Check" across three main topics: Computer Architecture, Native Language Programming, and Vector Mathematics.  The skills check follows a standard regiment of questions regardless of seniority, and as such focuses on the fundamentals of each topic.  Taking the time to answer these questions both helps us roughly judge where your strengths lie, and it gives us a sense for how you communicate about technical issues.
* Reflecting upon the skills check and discussion about how your strengths and desired growth fit into the team.

### Step 2: Take home programming test

The test is a mix of written questions and a couple of programming problems, and is open book (with citations and time keeping).

The written questions are designed to give us a sense of your written communication style, and to peek your opinion and experience on typical software development topics.

The programming problems typically take a couple hours and require you to think through some nontrivial (but hopefully interesting) design problems, and implement at least one fully functioning/tested solution.

### Step 3: An all day in-person interview

In-person interview days are, again, in two parts (so many things have two parts!!) and separated by a lunch break with team members:

The morning has two interview sessions with groups of Tools team members.

The afternoon has two interview sessions with groups of other engineers from the studio, and a final short interview with the studio CTO.

Each interview session has a break in between.  Feedback is gathered as the interview day commences, so your day may be cut short if the fit isn't there (why you interview with the Tools team first).

---

## Who leads the tools team at Infinity Ward?

Geoff Evans is a veteran of many AAA studios' tools teams, including:

* Insomniac Games (8 years)
* Neversoft Entertainment (2 years)
* Kojima Productions (2 years)
* Infinity Ward (since 2015)

If you have specific questions or concerns you can get in touch with him directly via [twitter](https://twitter.com/gorlak)

Geoff Evans was also the founder of The Toolsmiths, an online community of Tools Engineers. They mainly communicate through a Slack instance.  Feel free to DM your email address to [@thetoolsmiths on twitter](https://twitter.com/thetoolsmiths) for an invite, and join the discussion!
What are some resources for improving tools related skills?

You can read over a long list of skills and techniques that are good to know in [this gist](https://gist.github.com/gorlak/1a0747efe88c5e3998144c5787d090ec)

Also, there is a collection of GDC (and other sites) talks that go over interesting tools concepts in [this gist](https://gist.github.com/gorlak/f69c84adf4d70b04aad9)