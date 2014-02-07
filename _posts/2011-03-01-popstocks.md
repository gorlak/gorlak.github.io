---
layout: post
category : articles
tags : [postmortem, facebook]
---
{% include JB/setup %}

<em>This was originally published on <a href="http://altdevblogaday.com">AltDevBlogADay</a> in March of 2011</em>

<a href="http://twitter.com/gorlak">Myself</a>, <a href="http://twitter.com/andybrk">Andy Burke</a>, <a href="http://twitter.com/kramdar">Rachel Mark</a>, <a href="http://twitter.com/marcsh">Marc Hernandez</a>, and <a href="http://twitter.com/Pacman2k">Paul Haile</a> have built a Facebook game.  The goal was to spend 1 week building a game that would be fun enough and monetized well enough to grow into something that could generate some actual income.  While we aren't quite done with the game yet, I feel we have learned enough major lessons to justify a pre-launch Post-Mortem.

<a href="http://www.facebook.com/apps/application.php?id=201641909847723"><img class="alignnone size-full wp-image-1829" src="/assets/popstocks-logo-large.png" alt="" width="150" height="150" /></a>

<a href="http://www.facebook.com/apps/application.php?id=201641909847723"><strong>PopStocks</strong></a> models a stock market, but instead of companies we trade shares in Facebook Pages as items of value.  A Facebook Page must have at least 100,000 likes before we create a stock for it in our game, and the number of likes for that page dictates the total capitalization of the stock.  Every stock starts out at 25 Pops (our in-game currency), and its value on the market is dictated by market orders, limit orders, and rumors.  Rumors are actions that players can purchase with Facebook Credits, and can effect the market price of a stock.  PopStocks also has a store with power-ups that can prioritize a player's trades, provide advice on what to trade, and offer more in-game currency in exchange for Facebook Credits.  We are currently in the process (this very weekend) of adding more items to the store that are for players that want to redeem in-game wealth for real-world discounts and items.  We aim to be feature complete by Monday and just focus on polish and marketing buys through Friday.

<a href="/assets/popstocks-excited-small.png"><img class="alignnone size-full wp-image-1827" src="/assets/popstocks-excited-small.png" alt="" width="150" height="150" /></a>

<strong>What Went Right</strong>
<ul>
	<li><strong>Shared vision of what we were building.</strong> We spent our entire first day working through what the social and monetary implications were for all our ideas.  PopStocks ended up being the idea with the lowest cost to implement, lowest amount of art required, and had the highest potential for monetization.  The idea of a virtual stock market wasn't really new, but the idea of using Facebook itself as the source for stocks gave the entire team ideas about where to take our game.</li>
	<li><strong>Google App Engine as a development platform.</strong> Python and Google's data model classes (backed by BigTable) made fleshing out new functionality a breeze.  While the BigTable backend does have limitations with what is possible with its query construction, the fact that you can rearrange your data so quickly makes it an all around win.  Google has made specific choices about what features are available in queries for performance and scalability reasons, and deciding to prohibit inefficient features causes your design to iterate toward something that is more optimal than if you had every feature of SQL.  While Eclipse as an IDE and debugger leaves a lot to be desired (it suffers every trap of software too focused on plug-ins than unified workflow), its totally passible for a short project like PopStocks.  Deployment to Google's servers is a breeze, and the backend control panel has just about every feature you could need.</li>
	<li><strong>99 designs as a marketplace for art and iconography.</strong> From the get-go we wanted a game that was as minimal on art as we could get.  We are all engineers, and didn't have the cash on hand to put a lot of money into art.  We used <a href="http://99designs.com/">99designs.com</a> for only the important items we absolutely needed: a logo, a trophy icon, and some images of our broker character in various emotional states.  We put up a 3 day contest and didn't see much we liked until the very last day where we got a submission that just hit out-of-the-park what we were looking for.  For only a couple hundred dollars we had enough art to get to alpha.</li>
	<li><strong>Every developer uses a different platform and browser.</strong> The entire team had coverage of Mac, Linux, and Windows, and used Chrome, Firefox, IE, and Safari to develop features with.  This brought to light compatibility issues extremely quickly, and in general we were running in every browser all the time.  Despite Eclipse's usability drawbacks, it has functioned very well on all our development platforms.  We spent one day getting all our workstations configured as a full development environment and it hasn't been a time sink since.</li>
	<li><strong>Multiple builds of the game in Facebook. </strong>We setup 3 actual applications in Facebook: PopStocks-devel which points to localhost/127.0.0.1 (we run a local Google App Engine SDK server instance so we can debug the Python), PopStocks Playground which runs on Google App Engine but has debug features enabled, and the final game application: PopStocks.  Having Playground as an application in Facebook was invaluable for doing quick test passes before deploying to production.  We could also completely burn the data store on playground after hacking it up for various development reasons.  We did our initial testing with our friends on Playground before we were comfortable enough to launch the main game.  We didn't want to ever have to wipe the data store for the production instance.</li>
	<li><strong>Git and GitHub.</strong> Git is fantastic and GitHub makes it even better.  We worked exclusively through a GitHub private repo, which is only $25/mo.  We used its simple (but good enough) ticketing system for our issues as well as user-contributed bugs (contributed via a submission form in the game).  GitHub was only down for 30 minutes in the middle of one of the nights we were working.</li>
</ul>
<a href="/assets/popstocks-disappointed-small.png"><img class="alignnone size-full wp-image-1828" src="/assets/popstocks-disappointed-small.png" alt="" width="150" height="150" /></a>

<strong>What Went Wrong</strong>
<ul>
	<li><strong>Learning to program for the web.</strong> Everyone on the team had varying degrees of experience with developing for the web.  Andy had the most experience (with Google App Engine, Python REST APIs, and Javascript), and probably spent half of his time just talking through with the rest of the team how things should work, and pointing out which mistakes were worth correcting and which were not.  Time being a valuable commodity it is important to decide when to undertake refactoring passes (or when to burn entire features).  Keeping the game running is important sometimes, but not others.  We ended up building most of our features before doing any significant refactoring and then doing one massive reorganization to correct past mistakes several days before launch (last night).</li>
	<li><strong>Facebook authentication.</strong> To authenticate as a user logged into Facebook one must parse a 'signed_request' which is delivered in a POST on initial page load.  Being one of the first things your game would need to do, you would think there would be some handy example code in the official Facebook Python SDK.  Yeah, not so much.  Lacking good (or obtainable) documentation about what exactly a 'signed_request' is and how to parse one, it took quite a bit of digging to get it working.  Turns out there is a pending pull request to add the necessary code to the official Facebook Python SDK, but nobody at Facebook has accepted that request yet.  This burned about half a day... for something that should be documented as step 1 for building a Facebook application.</li>
	<li><strong>Facebook stability issues.</strong> Sometimes Facebook will take its sweet time to reply to graph API requests... and sometimes it's graph API code will timeout because the site just takes too long to reply.  Sometimes certain searches will break and fix themselves during the day.  We haven't found much visibility into how to monitor these stability gaps, you just have to take them in stride.  Also, if you use FQL, make sure not to use LIMIT 1 in your queries because sometimes it will just give you zero results instead of the first item you were searching for.</li>
	<li><strong>Internet Explorer being what it is.</strong> <a href="http://bits.blogs.nytimes.com/2010/09/17/a-loophole-big-enough-for-a-cookie-to-fit-through/">CP=HONK</a> is the magic that is needed to make iframe cookies work in IE.  Also, don't forget to omit the final comma delimiter in JavaScript lists.  Also, setTimeout().  We had to make more exceptions for IE than any other browser, by far.  Supporting IE 8 cost our project at least 1 man day.</li>
	<li><strong>GDC.</strong> The Game Developer's Conference happened right in the middle of our production period, but there wasn't anything we could do about that.  We lost more than half the team for an entire week which put a ton of work (and crunch) on a very few.  It also took attendees some time to hit the ground running after focusing on other things at GDC for an entire week.</li>
</ul>
<a href="/assets/popstocks-happy-small.png"><img class="alignnone size-full wp-image-1841" src="/assets/popstocks-happy-small.png" alt="" width="150" height="150" /></a>

<strong>Conclusion</strong>

Everything took longer than we thought it would, and we ended up tripling our 1 week deadline.  Feature creep contributed about a week to our blown deadline.  Halfway into the first week we saw that things were taking a long time to come online, and we started to worry that we wouldn't have enough compelling gameplay to be viable to even our friends (much less complete strangers).  If we had stuck to our original plan of a very minimal feature set we might have completed our game in two weeks, but it wouldn't be nearly what it is today.  Lets hope that bet pays off.