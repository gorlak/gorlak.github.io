---
layout: post
title: Insomniac Core Dump
tagline: Notes From the Conference
category : conferences
tags : []
---
{% include JB/setup %}

### 10am Intro to Day 1 ###
* This conference was inspired by Handmade con, borrowed the formula from them.  Mostly ad-hoc/interview style.
* Video from Ted Price about stuff, sharing with the community and whatnot.
* Mike answering questions submitted by attendees.  Rambling about his thoughts on small stuff.

Insoniac Core Team structure:

* Syndicate team
* Cinematics team
* Animation team
* Rendering team

### 10:30am Interview w/ Andreas Fredriksson ###

* Lead of Syndicate team (general tools team)
* Fleet of memory allocators, several different heaps (bookeeping data internal and external -- for GPU access)
* Things missing from debuggers: write strides of memory to a file, full system debugging
* Trials with transitioning to lead status from senior engineer
* Demoscene and c64 programming w/ the team over holiday break

### 11am Interview w/ Jonathan Garrett ###

* Lead of Animation/Audio/Physics team
* Node graph editor of animation driver, sounds intresting but a demo would be great

### 11:30am Panel ###

How does Insomniac manage build data?

Andreas F, Bob Sprentall, Jonathan Adamczewski

* Bob: The build aims it to be an invisible thing, no intervention
* Bob: Each asset type has a builder tool, invoke that builder once to build all assets of that type
* Bob: Build system in front of builder, after the build runs once, no up front dependency registration
* Bob: Worst case build with incremental build of changes due to dependency statting
* Adam: Recast navigation is globally optimized by loading all data to analyze spatial proximity
* Andreas: No file i/o in the game, only request load of asset-id, not file
* Andreas: Ddependency graph of all the assets the engine has loaded -> key assets
* Andreas: DG is stored off for disc mastering to know how to layout assets
* Mike: Asset-id is a hash, they see collisions
* Andreas: Don't use fios because DG loader has the info they need (serial loader working on known list)
* Bob: You mention BC7 is slow: did you consider streaming uncompressed textures?
* Mike: They use updater script to graduate/migrate source data to new schema
* Adam: They do have a cache, doesn't sound content-hash addressable, fetches entries that might not be relevant (has deps inside it)
* Andreas: Not sparse syncing, cordoning off departmental data, no task-specific condons yet
* Andreas: Feature development on the engine/tools take place on other branches
* Bob: Branches are used to gate release to production

### 1:30pm Panel ###

Reflections on >10 years at Insomniac

Jonathan Garrett, Giac Veltri, Chris Edwards

* Giac: Discrete asset types (mb, tb, lb) -> buildtool -> autobuild
* Jonny: Iteration time and 5s turnaround time, particles
* Chris: Asset tagging issues, not stored inside the actual assets, have to migrate separately
* Giac: Separate viewers for different asset types

### 2:00pm Interview w/ Dave Dimov ###

QA for tools/engineering group

* They do shadowing of users behavior with the tools, roll manual tests for quality
* QA team members to help champion usability of the tools, but appears not clear owner for measuring usability
* 3 week release cycle into all titles, a branch tracks the engine in each title
* Each game gets a full test pass through each code release

### 2:30pm Panel ###

Lessons Joining the Insomniac Engine Team

Vitor Menezes, Evan Hatch, Dale Kim

* A bunch of junior engineers being bewildered by fancy things

### 3pm Interview w/ Abdul Bezrati ###

* Draw calls, batching, shader variation management 
* Frame breakdown, deferred

### 4:30pm Interview w/ Elan Ruskin ###

* Callback to talk about forensic debugging
* Callback to GDC talk about statistics
* Physics and Havok rundown about how simulations don't necessarily make the best gameplay, how to compromise reality for each title
* The whole studio is making a game, and entertainment product, don't lose sight of that
* Use the tools is very instructive to improving them

### 5:00pm Interview w/ Chris Edwards ###

* LunaServer - hosts assets over an API, provides undo/redo
* Takes incremental changes and generates a journal of recent changes on that machine
* Changes live inside the memory of the server, Save is a feature of LunaServer, not the tools
* MongoDB stores all the assets, overly tied to the way it wants assets formatted
* LunaServer is coded with the formats that are relevant to the game, revising those formats need new build of LunaServer
* If choosing to do things over again would not use a database for persistence of assets, keep in memory
* Hooks for specific types of asset to invoke specific build steps and pass directly to the game

### 5:30pm Panel ###

What's our strategy and lessons learned wrt tools UI development

Andreas F, Chris E, Giac V

* Chrome auto updates were catastropic: Walk in monday and nobody can work
* Web browser environment too restrictive for the heavy lifting for game development
* JavaScript talent left the building and team members didn't want to learn
* Moving to all C++ tools, new and old editors go between LunaServer in realtime, verify each other
* Want more parallelism in the tools and build

### 10:30am Panel ###

How do we manage our streaming issues?

Abdul B, Bob S, Jonathan A, Carl-Hendrik Skarstedt (Yacht Club Games), Chad Barb (Respawn Entertainment)

* Defining the streaming problem
* Player movingment impacts the requirements for streaming
* Respawn mainly doing fixed size level and mip streaming, use HDD vs. optical

### 11am Interview w/ Giac Veltri ###

Guest: Matt Sharpe (Harmonix)

* Flash based node graph porting to native toolkit (Qt)
* Flash had scaling issues, could take multiple minutes to load, native took it down to seconds
* Matt: node based editors in Qt as well, for connecting events to the game (audio and visual effects) 
* JavaScript in the browser pain points, difficult contortion to get performance (manual cloning of template objects on anim frames)

### 11:30am Interview w/ Jonathan Adamczewski ###

Guest: Tony Albrect (Riot)

* Programming languages, native code and assembly
* 23m to build tools, engine, and game -> msbuild
* Tony: FASTbuild works well, not connected to DBS because of working remote

### 1:30pm Panel ###

What strategies do we use to approach debugging and profiling issues

Jonathan G, Tony Arciuolo, Elan R, Jonathan A, Guest: Tony Albrect

* Jonny: Macros that instrument the codebase for on-screen profiler, hooked into RAD's Telemetry
* Tony: Debug shader to draw skinned characters at bind pose in contrast color was very helpful in Sunset Overdrive
* Elan, Jon A: WPA is a fantastic tool for getting a lot of profile data
* Elan: Dota2? (referenced indirectly) has $500/microsecond of server CPU cost at scale

### 2pm Interview w/ Ron Pieket ###

Guest: Kalin (Funktronic Labs)

* VR headsets are annoying to take on and off when developing, TTY becomes less useful diagnostic tool
* Funtronic is making an RTS, experimenting with content creation in VR

### 2:30pm Panel ###

How do we manage iteration time (including for programmers)?

Jonathan G, Andreas F, Eric Li (Harmonix), Danielle Cerniglia (1st Playable Productions)

* Programmer iteration isn't frequently focus for compiler-writers, focus only on optimization
* Don't hesitate to implement workflow that cut entire departments out of iteration, free up human cycles

### 3pm Interview w/ Tony Arciuolo ###

Guest: Matt Pettineo (Ready at Dawn)

* Matt: What are the changes necessary to go multi-platform?
* Tony: Mostly software API issues and data organization issues to fulfill those APIs
* Matt: Any way to tie a performance issue back to content is very beneficial (shaders)
* Tony: A tricky issue is when env + lighting fight over who will benefit from complexity added to the scene, programmers arbitrate
* Matt: Tends to be lighters we arbitrate against becuase they are more technical, make them pick their battles
* Tony: GI solution builds distributed across the studio

### 4:30pm Panel ###

How do we approach VR?

Abdul B, Bob S, David Neubelt (Ready at Dawn)

* 4-5m per viewport to draw the whole scene to keep framerate for VR
* No motion blur, not god rays, etc... due to time
* Draw good stuff, not more stuff

### 5pm Interview w/ Bob Sprentall ###

Guest: Garret Foster (Ready at Dawn), Chris Butcher (Bungie)

* Mike wants to talk about build systems
* Chris: Need good solution for quickly sharing changes with neighbor devs
* Garrett: Can't have multiple copies of the game on a hard drive, 2TB #head
* Garrett: Custom p4 client views for different departments
* Bob: Mongodb stores build metadata, moved into in-memory db (LMDB)
* Bob: Cache was a side-project, scaled up out of side-project status, web-tech based cache didn't perform well (mongodb/web-req)
* Chris: Build dependency graph is pretty arbitrary, no hard lines between assets, heavy interdependence
* Chris: Constant buffers built for environment interdepend bi-directionally on the shaders used to draw them, heavy build cost
* Chris: Requiring more dependencies between assets shouldn't necessarily be low in cost to prevent interdependence
* Chris: A big regret is not seeing PC-centric content creation; would have made differenc choices since it has better random asset access
* Bob, Garret: Pipeline has platform-independent step, then a platform-dependent step
* Bob: Doubled up on processing identical data, took action to avoid it, but added on after the fact
* Chris: Metadata for all the assets is several GB in and of itself
* Chris: The cache is pretty monolithic, corresponds to a single changelist (stable builds are identified and encouraged to sync)
* Chris: Unstable/bleeding edge builds are available but more costly to sync to because caching isn't complete
* Garret: No toleration of missing assets, hard error; tolerating missing assets could lead to subtle bugs, skipped reporting tools to police
* Garret: Building data in debug (w/ scribbling, init of memory pages) and in release and compare helps find determinism bugs
* Garret: CI builds and populates the cache as changes commence
* Garret, Bob: Peers/users do push data into the cache, problematic machines are found and fixed, breadcrumb left in the cache entry
* Chris: Spatial relationships are important for caching, esp for caching baking problems (lighting)
* Chris: Content creators always want shipping quality of the bakes
* Chris: Bias toward baking techniques toward cachable solutions (umbra)

### 5:30pm Panel ###

What internal tools have we needed to develop to solve engine or tools related issues?

Jonathan G, Andreas F, Bob S, Jim Hill (Dreamworks Animation)

* Bob: Use the web-tech architecture of LunaServer to connect to the backend of users to look at build system state, make fixes
* Andreas: <a href="https://github.com/deplinenoise/ig-memtrace">Trace</a> all the memory traffic and callstacks, transports over the network to a tool where you can scrub events
* Andreas: Custom events at level loads, etc... tool allows you to see previous memory owners
* Andreas: You need to get outside the comfort zone of your language of preference, doesn't matter what it is





