---
layout: post
category : articles
tags : [c++, memory leaks]
---
{% include JB/setup %}

Badly-behaving third party libraries sometimes allocate heap memory into global variables (or private static member variables).  This can cause the shutdown leak check to dump false-positive leaking objects.  They get in the way when trying to track down which allocations in your code aren't properly matched with a free.

Fortunately the shutdown leak check is really just calling some other debug CRT functions, which are exposed to the user:

	// capture debug heap state before doing stuff
	_CrtMemState startState;
	memset( &startState, 0, sizeof( startState ) );
	_CrtMemCheckpoint( &startState );

	// do stuff!

	// dumps leaked blocks to the IDE's Output window
	_CrtMemDumpAllObjectsSince( &startState );

	// capture debug heap state after doing stuff
	_CrtMemState endState;
	memset( &endState, 0, sizeof( endState ) );
	_CrtMemCheckpoint( &endState );

	// assert that memory wasn't leaked
	_CrtMemState diffState;
	memset( &diffState, 0, sizeof( diffState ) );
	_ASSERT( !_CrtMemDifference( &diffState, &startState, &endState ) );

This gives you the dump you expect during shutdown, but somewhere in your app where you can be more confident that every leak reported is a valid leak.  It also generates an assert so you can trap in the debugger as soon as a memory leak was detected, a very handy thing for keeping your code honest in the long term.  Keep in mind that other threads' allocations will be included in this, so pick where you check for leaks very carefully!