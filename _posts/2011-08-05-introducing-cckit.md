---
title: Introducing CCKit
description: "Introducing a framework that brings some fancy UIKit feature to Cocos2D."
categories: Technical
tags: [Cocos2D, UIKit, Objective-C, iOS, programming, CCKit]
published: 8-5-2011
---

While working on various Cocos2D-based projects, I came to the point where I needed to implement the Cocos2D equivalent of a `UIScrollView`. While we found a few community-created solutions, none of them really matched the intuitiveness and fluidity of Apple’s own `UIScrollView`.

What I’m going to talk about in this post is a new set of classes I’m calling *CCKit*. The purpose of the library is to bridge the gap between Cocos2D and Apple’s *UIKit* classes. The library includes classes like `CCScrollLayer` (`UIScrollView` emulation) and `CCTableLayer` (`UITableView` emulation) which should give very close approximations to the UIKit equivalents. Additionally, the library is [open source and hosted on GitHub][cckit], so feel free to include it in your own projects and even contribute if you’d like.

## CCScrollLayer

The `CCScrollLayer` class (and both `CCTableLayer` and `CCTableLayerCell` classes) are derived from code written by Sangwoo Im in his [CCTableViewSuite](http://www.cocos2d-iphone.org/archives/943). The original code has provided a lot of Cocos2D-based games with good `UITableView` approximations. While the table view implementation feels pretty good, I felt that the scroll view implementation could go a bit further.

The `CCScrollLayer` class in CCKit supports nearly all of the features of Apple’s `UIScrollView` class, including bouncing, deceleration, and pinch-to-zoom. There are a few features missing (such as paging and directional lock) that will be added over time. I spent a lot of time playing around with values trying to get the bouncing and deceleration to feel correct. The zoom also properly mimics zooming in and out from the centroid of the two touches on the screen instead of just from the center of the screen (which is a personal pet peeve of mine).

Panning and zooming are implemented with gesture recognizers. I apologize to those wanting to deploy for targets below 3.2, but the benefits of using gesture recognizers are too numerous. For one, you can use your own gesture recognizers in cooperation with the recognizers for the scroll layer, and even set up dependencies between them (i.e., don’t pan if a long press is detected first).

Other than the aforementioned features, a `CCScrollLayer` works pretty much like any other `CCLayer`, which means you can add all of your sprites (and even other layers) to it. Here’s how to set one up:

{% highlight objc %}
CCScrollLayer *scrollLayer = [CCScrollLayer scrollLayerWithViewSize:[[CCDirector sharedDirector] winSize]];
scrollLayer.contentSize = CGSizeMake(5000, 5000);
 
// Add whatever you want to the scroll layer using addChild:
 
// Add the layer to the scene.
[scene addChild:scrollLayer];
{% endhighlight %}

## CCTableLayer

`CCTableLayer` is still a work-in-progress, and as such isn’t ready for primetime yet. We need a `UITableView` implementation for our game, though, so it should be coming soon.

## CCGestureRecognizer

This class was originally written by [Joe Allen](http://www.cocos2d-iphone.org/forum/topic/8929). Most of my changes involved cleaning up the public interface so only the functionality necessary to use the class was exposed. The original implementation also required modifying code in `CCNode`. I’ve removed that requirement by putting the changes in a category extension.

The class essentially acts as a wrapper for a `UIGestureRecognizer`. In fact, the only real functionality this class provides is the ability for your gesture recognizers to respond to node touches instead of just view touches. Now, for instance, you can set up `UITapGestureRecognizers` on your sprites and have each sprite respond to the tap only if it was touched.

Here’s how to set one up:

{% highlight objc %}
- (void)onEnterTransitionDidFinish
{
   [super onEnterTransitionDidFinish];
 
   UITapGestureRecognizer *tapGestureRecognizer = [[UITapGestureRecognizer alloc] init];
 
   // Set up the tap gesture recognizer like you normally would.
 
   // You'll want to retain ccRecognizer for later use.
   CCGestureRecognizer *ccRecognizer = [CCGestureRecognizer recognizerWithRecognizer:tapGestureRecognizer target:self action:@selector(handleTapGesture:)];
   [self addGestureRecognizer:ccRecognizer];
}
 
- (void)onExit
{
   [super onExit];
 
   [self removeGestureRecognizer:ccRecognizer];
}
 
- (void)handleTapGesture:(UITapGestureRecognizer *)gestureRecognizer
{
   // Do something when this sprite/node is tapped on!
}
{% endhighlight %}

If you look in the `CCScrollLayer.m` file, you’ll see that it’s using the `CCGestureRecognizer` class to set up its `UIPanGestureRecognizer` and `UIPinchGestureRecognizer`.

## More To Come

For the time being, the code is made with ARC in mind. If you try to run this in a non-ARC project, you’ll probably leak memory everywhere. Apple has made it clear that ARC is the future of iOS development, so a non-ARC version will likely not be coming (from me anyways).

As mentioned before, this library is a work-in-progress and will be added to on a constant basis. I can tell you that from our end, we’ll add things as we need them in our own games. However, if you have something you’d like to contribute, then feel free to send a pull request. Just remember that the library is solely for mimicking or allowing access to Apple’s UIKit functionality in Cocos2D.

[cckit]: http://www.github.com/jerrodputman/cckit