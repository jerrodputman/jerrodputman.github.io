---
title: "Postmortem: Sheepstacker"
description: "A discussion about the development of the iOS game Sheepstacker from Tiny Tim Games."
categories: Postmortems
tags: [Tiny Tim Games, Unity, indie, iOS, postmortem, game development]
published: 3-21-2009
---

*NOTE: The following postmortem was originally presented on the Tiny Tim Games website, and it discusses a game that is no longer available to purchase or download. It has been placed here as a reference.*

As Sheepstacker has been available in the App Store for over a month now, I thought it might be time to share a few of our experiences from working on the game. If you’re a budding indie game developer, or you’re just interested in how we do the things we do, then I hope you’ll enjoy the following “postmortem” on how we felt the development of Sheepstacker went.

## Necessity… Mother… Invention…

Sheepstacker was a game of necessity for us. I had been working as a gameplay programmer in the traditional gaming industry for over six years by the time we had begun work on Sheepstacker. For a long time, I had felt the burn-out from either working too many hours or simply working on projects that either had no end in sight or just weren’t all that interesting. But that’s how things go in the big console and PC industry; you’re always working on someone else’s game, which is fun for a while. However, if you have any creative ambition at all, eventually you want to do something more.

In addition to this, the industry itself began going through radical changes. The Wii rose to the top as the dominant console in the marketplace (much to the chagrin of many top-tier publishers and developers), and the entire industry seems to have made a shift from being exclusively hardcore to something everyone wants to do. Couple this market shift with an economic recession, and you’ve got an industry that looks healthy overall, but has lots of internal problems at some of the once-successful companies. I was a victim of this shift, as were [many][layoffs1] [others][layoffs2] in the [Great][layoffs3] [Tech][layoffs4] [Layoffs][layoffs5] of 2009.

So with pink slip in hand and the hiring market drying up all across the industry, my wife Shannon and I set out to achieve the indie game development dream. Our number one priority was to simply get something done and out there with the shortest development time possible. We needed three things to get this accomplished though: 1) a platform; 2) an engine or SDK; and 3) a game design.

The first two were easy. Shannon and I both had iPhones, and I had switched fully to Mac about two years ago. We had everything we needed to get going with the iPhone SDK. As for the engine, I had already been messing around with the desktop and iPhone versions of [Unity][unity] for a while at that point. I really liked the workflow advantages of Unity, and felt it was directly engineered toward making great playing and great looking games very quickly. We still needed a game design though.

I had at one time played [Digital Chocolate][digital-chocolate]‘s great mobile game Tower Bloxx. I was so impressed with its simple one-button, addictive gameplay that I wanted to do something similar, but add to it and put my own little twist on it. However, I was having trouble coming up with a theme. That’s when Shannon suggested that we stack sheep. The rest is, as they say, history.

The following are some of the top five things that went right and wrong during the development of Sheepstacker:

## What Went Right

### 1. The Unity Engine
As an indie starting out with Unity, you already start way ahead of the curve. While the editor in Unity is sleek, simple, and a pleasure to use, the real advantage with Unity comes from the myriad things it does to try to keep you focused on making the game, and not worrying about everything else.

Take, for example, importing assets. To import a texture, you simply save the Photoshop file into the Assets folder of your project. After setting a few import settings, you never have to worry about importing the asset again. Simply open up the file, edit it, and save it. Unity automagically detects the edited file and reimports it. And that’s it.

The same goes for the scripting system. Scripts are auto-compiled on save, so you don’t have to worry about and older build lying around. In addition to this, it’s extremely easy to expose variables to the editor, allowing you to not only tweak settings, but also tweak them while the game is running and immediately see the effect in-game. It also helps that the scripting system supports writing scripts in C#, which is just close enough to C/C++ for me to be comfortable.

Being able to run the game in the editor without having to export constantly to the iPhone was great as well, though the export process is fairly straightforward and automated. From input to output, Unity streamlined our development immensely and allowed us to finish Sheepstacker, from initial prototype to app submission, in only three weeks.

### 2. A Simple Game Design
The simple, one-button gameplay of Sheepstacker meant that we had a small, well-scoped game for our first time out on our own. We couldn’t possibly do a larger game or even a simple level-based game as it would simply require too much content. Neither of us had really done 3D artwork before, so we needed something that wouldn’t be demanding content-wise.

We also needed something we could keep the reigns on in case feature creep started to kick in. The prototype for Sheepstacker was finished the same night we came up with the design, and the basic swinging/stacking mechanic changed very little from start to finish. Because we had the core of the game up and running so quickly, and because it was immediately fun, we felt that we could continue adding to the game until we felt like we were done, and not have to struggle with the basic gameplay while also adding all of the bells and whistles to make it polished.

### 3. Blender
While to serious artists in the gaming industry, [Blender][blender] might be an odd choice, for someone who had no prior experience in 3D art, it turned out to be one of our most useful tools.

I had purchased [“The Essential Blender”][the-essential-blender] book nearly a month prior to beginning Sheepstacker in an effort to take the fact that we couldn’t do 3D artwork ourselves out of the equation. While the interface was initially very daunting, with some good tutorials in the book, I was able to fairly quickly overcome the learning curve and become rather confident in my own ability to create 3D art.

Pretty soon, I was wanting the Blender shortcuts and tools to start appearing in other programs. Once you’re acquainted with the intricacies of the Blender workflow, you realize it’s designed for speed, creating things and manipulating them as quickly as possible. Again, this is one of the things we needed most on our project, and Blender delivered in spades.

### 4. Playing To Our Strengths
While I’m a programmer by trade, I had always wanted to get into the realm of game design. While one could simply chalk this up to a case of a programmer thinking he can make a better game than the designers, I had actually spent my entire career paying very close attention to the design aspects of the things I worked on. I regularly spoke with designers to get their insights into why some things work and why some things didn’t work. I would listen to the results from focus tests. I would pass ideas by designers themselves to get their input on it. My goal was always to learn about games beyond what I was simply implementing.

As it turns out, both experiences paid off for Sheepstacker. I had the experience of a seasoned programmer and enough game design knowledge to be able to quickly see when ideas would or wouldn’t work. For instance, we had a black sheep in our game at one point. It was meant to be something that the player should dodge, to kind of mix up the gameplay a bit. However, it never quite felt right. In the end, I’m glad to say that I think the addition would have eventually become a subtraction overall to the simple, addictive gameplay we created.

Additionally, Shannon was able to do what she had been doing for a long time already: graphics art. While I was able to come up with some fairly respectable 3D models, I was absolutely terrible at coming up with textures for them. However, she was able to use her experience and knowledge to fill that gap. I think we nailed the whimsical, cartoony visual style we were going for, and a lot of that can be credited to her.

### 5. The Sheep
While this could have gone under the “Blender” heading, I wanted to specifically point this out. We had the prototype running for a few days before we put the sheep into the game. It was already fairly enjoyable at that point. But as soon as we put the sheep in, without animations or even textures, all of a sudden we had a laugh out loud funny and fun game. The first time we saw a sheep land too far to the side of the stack and roll off, I knew we had something special.

And it only got better. After we textured and rigged the sheep, I began playing around with animating it. I was surprised at how simple, subtle movements really added so much to the game. The way that the sheep’s ears flap in the wind as they fall, the bulgy eyes when they’re knocked from the stack, the simple plop they do when they land, these all added the sort of polish we needed to really make the game stand out when people saw it. If there was one thing we couldn’t mess up, it was the sheep, and I think we did an admirable job with them.

## What Went Wrong

### 1. No Lite Version At Launch
It was a mistake not launching the game with an accompanying “lite” version. What’s an even bigger mistake is that it took us so long to final make one and submit it. We knew we had a great game. We had done numerous focus tests, and everyone who played it loved it. That meant that everyone else would love it too, right?

Unfortunately, people have to play the game first before they realize just how fun and addictive it can be, and without a lite version, we had to rely solely on word of mouth. And being that we’re a tiny independent game developer doing all of this from our home office, there was simply no way to generate the word of mouth buzz to make the game successful. People need to try it for themselves. That’s a mistake we’re not going to make again.

### 2. Where’s The Twist?
While I initially stated that I wanted to do something like Tower Bloxx, but with a twist, unfortunately the “twist” simply became “look, I can stack sheep!” While we have lots of subtle but important gameplay tweaks to the formula, there’s nothing we can really point to and say, “Yes, it’s similar to this, but wait ’til you get a load of this!”

Thankfully, we added a bit of this in our 1.1 update with the “Baa-lance” mode. While there are other “tilt the iPhone to catch something” games on the market, we essentially took the classic stacking gameplay (including the combos and the penalties) and made something that was unique and fun.

### 3. No High Scores
This is one that I was beating myself up for all the way through the submission process. However, we simply couldn’t get it into the initial version of the game within the timeframe we were on. It turned out to be a highly requested feature, so I’m glad we were able to add it finally (along with the inclusion of badges).

### 4. Making Menus
While the menus and UI itself turned out really nicely in the game, the actual development and upkeep of them took far too much time out of our schedule. Unity doesn’t have a very good one-size-fits-all solution for UI, and maybe no engine can provide that. But even so, having to deal with the slightly archaic `GUITexture` and `GUIText` objects was neither quick nor easy.

I will say this, though: The menu system from Sheepstacker is highly portable. We should be able to bring it into our projects going forward with little to no upkeep required. So this negative definitely turned into a positive going forward.

### 5. Sound
I hate sound effects. More specifically, I hate finding and implementing sound effects. While sound effects are most definitely a necessity in any good game and can many times steal the show, I just simply do not have the knack for finding the right sound for the right occasion.

We scoured through hundreds, if not thousands, of sounds before we finally came up with the twenty or so sounds that finally made it into the game. In addition, it took us a long time to find the right music for the title screen. While we had found the in-game music relatively early on and were really pleased with it, we just couldn’t settle on the title screen music. The music we have now is sufficient, but I would have liked to have had something more thematically similar to the great music found in the game proper.

## Something New

We learned a lot of valuable lessons going forward from Sheepstacker. We’re immensely proud of what we created, and we think it’s an excellent game that’s simple enough for a wide variety of people to be able to pick it up, play it, and immediately identify with it and enjoy it. However, I think we can do a lot better, and I look forward to us doing just that on our future projects.

[layoffs1]: http://feeds.gawker.com/~r/kotaku/full/~3/mHMfD_9WXE0/lay-offs-strike-crystal-dynamics
[layoffs2]: http://feeds.gawker.com/~r/kotaku/full/~3/uPDuEH6pKi8/more-ea-cuts-go-live-today
[layoffs3]: http://feeds.gawker.com/~r/kotaku/full/~3/OyTb5APfXuA/amd-cutting-jobs-slashing-survivors-pay
[layoffs4]: http://feeds.gawker.com/~r/kotaku/full/~3/9XPsE4CnawI/sega-staff-cuts-confirmed
[layoffs5]: http://feeds.gawker.com/~r/kotaku/full/~3/-xmbg06okT4/flight-simulator-devs-grounded-by-microsoft-job-cuts
[unity]: http://www.unity3d.com
[blender]: http://www.blender.org
[the-essential-blender]: http://www.blender3d.org/e-shop/product_info.php?products_id=96&PHPSESSID=db3c31556dac7ec5d1a6f0ff8cb7a22c