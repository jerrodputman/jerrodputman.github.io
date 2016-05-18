---
title: "Postmortem: Word Monkey"
description: "A discussion about the development of the iOS game Word Monkey from Tiny Tim Games."
categories: Postmortems
tags: [postmortem, Unity, indie, iOS, Tiny Tim Games, game development]
published: 9-29-2010
---

*NOTE: The following postmortem was originally presented on the Tiny Tim Games website, and it discusses a game that is no longer available to purchase or download. It has been placed here as a reference.*

It’s once again time for us to take a look back at the development of one of our games here at Tiny Tim Games. This time, it’s Word Monkey, our second game. We’ve been pretty silent of late due to lots of cool stuff that we’ve been working on. I recently joined the [#iDevBlogADay][idevblogaday] waiting list, and figured it was finally time to see how things went for our little game about words and monkeys.

## The Indie Life Is A Hard Life

Before jumping into the what-went-rights and what-went-wrongs, I should start by giving a little bit of what life was like between the release of [Sheepstacker][sheepstacker-postmortem] (our first game) and Word Monkey. While it may look like it took us a year to make a small game like Word Monkey, the truth is a lot of things happened before our little word game even crossed our minds.

While I still feel like Sheepstacker as a game was a success from the standpoint of releasing a game all on our own, it wasn’t successful enough to pay the bills. Indeed, I had to take up a lot of contract projects, and even that wasn’t enough. So my wife and I had no other alternative but to move in with some relatives. There begins the long journey to the second Tiny Tim Game.

I think we went through about half a dozen different prototypes (of which I’ll write about in a later post). Some of them were good but weren’t feasible for the two of us to do alone; some of them were bad and the less said about them, the better. The problem was that with the relatively uneventful development process of Sheepstacker, I had gotten it into my head that we could make a much bigger game. Not so.

Then, one fateful day, my wife Shannon suggested that we make a word game. Which leads us to…

## What Went Right

### 1. The Prototype
When Shannon suggested we do a word game, I thought it might be a nice break from trying to create my epic sprawling spherical-world dual-stick shooter (no joke). We talked at length about various gameplay mechanics, and had a few interesting (if convoluted) ideas.

The big breakthrough came when I started thinking more along the lines of Tetris or Bust-A-Move. The idea was that there would be a jumble of letters rising from the bottom of the screen, and the player must tap on letters to spell words. When the player successfully spells a word, the letters the player had tapped disappear, leaving more room at the top. If any of the rising letters hit the top of the screen, it’s game over!

It sounded pretty good in my head, so I fired up [Unity][unity] with the intent to make a prototype to see if it was actually any fun. After just a single day of programming, we had our first taste of Word Monkey (which, admittedly, doesn’t sound very yummy). While it didn’t look like much, most of the code in that prototype is still in the final release of Word Monkey. The primary gameplay mechanics were in place. All we needed was a theme.

### 2. The Characters
While gameplay is king, I do feel that adding a touch of character to gameplay allows the mechanics to be more easily digestible. Thus we set out to find an appropriate theme for our game (which was tentatively titled “Word Up” at this point… very clever).

Originally, we had thought about doing some sort of glossy, Web 2.0-ish style interface so that it would reach a wider audience than our first game (which is only liked by sheep enthusiasts, I’m sure). However, nothing we tried really felt right, and it all just ended up looking boring or uninteresting. Though, I will say we did keep the blue letter blocks from some of these early designs.

I really felt we needed a proper theme, and we went through everything from volcanoes (rising letter lava rocks) to water pipes (rising letter, umm, water blocks). Nothing was really sticking. Then, one day while I was in the shower, it hit me… Word Monkey! I jumped out of the shower, ran to my wife, and simply said, “Word Monkey!” She immediately knew what I was going on about.

It took about two months from the creation of the prototype to that theme though, during which time no progress was made on the game at all. But once we settled on the theme, everything came into place.

We created some cute fiction that had the player programming words into the brain of a monkey. From that idea came my favorite character in the game, Dr. Schmachter, who guides the player along. The theme continued to add more and more value to the game as we developed it, and I think it elevated the game from a simple word game to something much more special.

![Dr. Schmachter and the monkey][image1]

### 3. Doing It All Ourselves
Little known fact: The sheep in Sheepstacker is actually a (heavily modified) model I purchased off the Internet for $5. With Word Monkey, though, we did everything. We designed the game from the ground up; I modeled, rigged, and animated the monkey and the doctor; Shannon took care of the textures and interface.

We even made our own music! Sure, it’s all stuff you can make in GarageBand, but it was satisfying. And since it was custom made, it fit perfectly with the game. Our vision is exactly what you see in the game. No more, no less.

### 4. Multiplayer
While we don’t have any statistics to back this up, multiplayer worked out better than I had originally anticipated. The idea was to have a Puzzle Fighter sort of mechanic where you could rain letters down onto your opponent.

One twist on this gameplay mechanic was to turn it into a sort of tug-of-war match. The bar at the bottom of the screen lets the player know how close he is to sending a barrage of letter blocks to his opponent (or how close he is to receiving the same). This makes the gameplay extremely frantic as you have to balance spelling smaller words to avoid getting a row of letters dropped on you with the fact that vowels are in short supply.

In addition to the featured multiplayer mode of the game, one unintended multiplayer mode crept into the iPad version of the game. Recently, some of our family had set the iPad down flat on the table and began playing. They would shout words that they find, reach across the table to spell words, and scream as the letters got ever closer to the top. It was great to see the game being played in a completely new way.

### 5. Free Game Of The Day
Word Monkey was featured in OpenFeint’s Free Game Of The Day promotion, and was downloaded 20,000 times. Up until that point, sales had been dismal, but after the promotion the game’s sales increased significantly:

![Sales before and after the FGOTD promotion][image2]

But that’s not all. About a month after the FGOTD feature, Word Monkey was inexplicably mentioned on Glenn Beck’s radio show! Love him or hate him, our sales skyrocketed! Apparently, one of his kids was really into the game. Since I know the sales figures from before the FGOTD promotion, I’m pretty sure his kid grabbed the game while it was free. While I wouldn’t bank on that happening for all of our games, that just goes to show that word of mouth is indeed very valuable. You never know who might be playing your game!

## What Went Wrong

### 1. Fatigue
Full-on production of Word Monkey took roughly two months. During the last month of that production, I essentially woke up, worked on the game, and went back to sleep. The constant work eventually caught up with me, and by the end I was severely suffering from fatigue. Hence, most of the remaining what-went-wrongs can be traced back to this. I was so tired and just wanted the game to be finished that a few things that should have received more polish or a bit better design didn’t.

To be honest, I’m not entirely certain why I did it, since I had no boss to answer to and we weren’t on any particular deadline. I was just excited about the game, and wanted to get it released as quickly as possible. Part of the reason could be because there was such a long time between Sheepstacker and Word Monkey. But even so, I’ve learned that the best thing you can do for your game is to take care of the person/people making it!

### 2. Dismal Launch
By the end of the second week of Word Monkey being available on the App Store, it had reached a version number of 1.0.6. That’s six updates (and in case you’re wondering, that’s back when Apple was much quicker with approving updates). The initial release had debug text listing the frames per second right in the middle of the screen. Subsequent releases had everything from power-ups that crashed when you tapped on them to level XP requirements that would take several lifetimes to actually achieve. It was a mess.

By the end of production, I was just so ready to put the game to rest that I rushed the release way before it had proper quality testing. This is something that is unacceptable to me, and it won’t be happening again. I can’t say that all of our future games will never have any bugs, but they’ll be the type of bugs where you have to jump in an obscure corner 500 times to get them to occur.

### 3. Visual Feedback
I feel like we hit a home run with the visuals of our game. We went with a minimalist design with mostly yellow backgrounds and nice interface widgets. Unfortunately, not all of the important information is properly conveyed to the player.

For instance, many of the power-ups in the game last for a certain number of seconds (i.e., the “fire” or double score power-up doubles any points you receive while it’s active; you can even stack them). However, there’s no way of telling which power-ups are currently active. A few options had been considered for how to display this information, but ultimately fatigue reared its ugly head again, and the issue was simply never resolved.

### 4. Interface Confusion
Due to the limited screen space on the iPhone, we had some difficulty placing all of the information we needed to display on the screen (which is one of the reasons for the power-up issue we had above). One way of creating more space for the actual playing field was to combine the label of the word the player is spelling and the button they need to tap to submit the word. I prototyped it up, we played it, it was fun. Done.

Unfortunately, I neglected to do proper usability testing with people unfamiliar with the game like we had done with Sheepstacker. What usually ends up happening when someone first starts playing the game is that they spell a word and then hit the backspace button. We’ve even gotten e-mails from people who had obviously tried long enough to write us an e-mail!

![Our messy tutorial screen][image3]

There were a few ways we could have solved this problem, but I think the best way will be for us to simply place a pop-out underneath the button every time the game starts, not just the first time. You never know when a new player will pick up a friend’s device and start playing your game.

### 5. Not Enough Monkeying Around
Again, due to fatigue, there simply aren’t enough monkey animations in the game! I’m not exactly a skilled animator, so the animations that are in the game weren’t exactly easy to come by. I think the point gets across fine, but so much additional character could have been added to the game if we just had more monkey animations!

On the flip side, I probably over-animated Doctor Schmachter. He has around 10 unique animations, and these are only seen during the intro. I’m extremely proud of those animations though, as I feel they worked really well with the character’s dialogue. But in the end, resources and time were spent in the wrong area.

## The Indie Life Is A Great Life

After all is said and done, we still managed to have a very fun, funny, and unique game that we made all on our own from our bedroom. My commute to work was literally five feet, and there was no hot-shot, middleman producer dashing all of our design dreams. The game we made is the game we wanted to make, and I’m extremely thankful that we were able to do it.

We’re deep into production on our third game, and I’ve already taken what I’ve learned from my experiences with Word Monkey and am putting those lessons into practice. I still work when I feel inspiration. But if I feel tired, I just get up, go into a room without a computer in it, and just relax. Or I play other people’s games! What a novel idea — a game developer playing games!

[idevblogaday]: http://www.idevblogaday.com
[sheepstacker-postmortem]: /2009/03/21/sheepstacker-postmortem/
[unity]: http://www.unity3d.com
[image1]: /images/2010-09-29/1.jpg
[image2]: /images/2010-09-29/2.png
[image3]: /images/2010-09-29/3.png