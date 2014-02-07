---
layout: post
title: Code Organization Patterns
tagline: Be Kind to Your Revision Control
category : articles
tags : [code organization]
---
{% include JB/setup %}

<em>This was originally published on <a href="http://altdevblogaday.com">AltDevBlogADay</a> in June of 2011</em>

Lately I am settling into a new job over at Neversoft.  There are some awesome folks over there, and I am really enjoying it so far.  Along with starting a new job comes learning a completely different codebase.  This can be especially arduous for tools folks since tools code typically sits atop a mountain of engine, pipeline, and foundation code.

In trying to wrap my head around an entirely new chunk of tech, I keep re-discovering patterns that make it easier to get your bearings on a lot of new code quickly.  There are lots of these patterns that studios follow when organizing their code, and following these can make it easier to dive and and start getting work done (or just make getting work done in general).  Some or all of these may be obvious to experienced engineers, but I figure it never hurts to reinforce best practices, and you never know when someone will have the total opposite opinion for really interesting reasons.

<strong>Maintain just a handful of high level solutions so its easy to gain grand perspective.</strong>

The lower the solution count in your project the better.  Ideally they should all be in the top level folder of your code.  The key here is to create awareness of the major chunks of technology in your project.  I think most people agree that the bar should be low for any engineer to get in and look at tools, engine, or game code.  The more you hide solutions within your code tree the more arcane knowledge is required to even know who the major players are in your codebase.

<strong>Direct all compiler output to a single folder.</strong>

Nothing hurts broad searches more than having large binary files mixed in with the source you are trying to search.  It's probably the reason why Visual Studio has preconfigured laundry lists of source code file filters in their Find in Files tool.  If you redirect all your compiler output folders to its own root folder then broad searches gets orders of magnitude faster since it doesn't have to wade through compiler data.

If your compiler output is directed to a separate dedicated folder then doing a clean build is just a simple matter of destroying the output folder and re-running your build.  Explicit cleans are just slower, and its just easier to delete a folder when scripting things like build server operations.

Code generated via custom build steps counts as compiler output too!  Add your output location an include path and #include generated code, even c/cpp files.  Doing this keeps a very clear distinction between generated code and code which belongs in revision control (and hopefully you aren't storing generated code in revision control!).

<strong>Keep 3rd party library code and solutions separate.</strong>

A big part of effectively searching through your codebase is being able to differentiate your code from external library code.  Littering 3rd party libraries in with your own code can muddle search results.

Frequently its not necessary to clean build both 3rd party code and your project code, so having separate solutions can save time.  It also makes performing search and replaces within solutions that only have your project code in them safer (you don't want to search and replace within a 3rd party lib do you!?).

<strong>Install large 3rd party SDKs directly onto workstations.</strong>

<strong> </strong>Revision control isn't the only software delivery mechanism on the planet.  Nobody should be making changes within the CellSDK, DirectX SDK, or FBX SDK so they shouldn't be checked into revision control.  These packages tend to be very easy to script for unattended installation (msiexec).  This makes it easy to write a simple SDK checkup script to make sure that any given client (even build servers) have the latest kit installed.

Most large SDKs have environment variables that make them easy to find on the system, and even if they don't you can typically assume where it should be installed.  If they are missing it's a simple thing to track down and install t (even for junior or associate engineers).  Also, it never hurts to add compile asserts to validate that the code is being built against the correct version of those libraries.

If you happen to develop on a system with a package manager, they are awesome for making it easy to pull down 3rd party libraries directly off the internet.  Microsoft's <a href="http://coapp.org/">CoApp</a> project aims to do just that on Windows.

<strong>Only check in binaries of what you cannot easily compile.</strong>

The less compiled binaries you check in the better your revision control will perform, and everyone you work with is served better when revision control works well.  Source code is much quicker to transfer and store on servers and peers.  Not checking in compiled binaries means less waiting for transfers, less locking for centralized servers, and less long term size creep for distributed repositories.

Checking in built versions of libraries will create a headache for yourself in the future when you want to deploy a new compiler or support a new architecture (which will require you to recompile using a bunch of crusty project files that haven't been used in months or years).  It's always worth a little extra time when adding a new external library to take command over your build configuration management.  Sometimes this can involve making your own project files instead of using ones that may be included with the library source code.  High level build scripting tools like Premake, CMake, and boost::build are worth spending time to learn, and can make hand-creating IDE-specific projects seem archaic.  If updating external libraries in your engine is easy you will do it more often, and hence reap the benefit of more frequent fixes and improvements you don't have to do yourself.