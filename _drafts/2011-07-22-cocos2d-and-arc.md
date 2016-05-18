---
layout: page
title: Cocos2D and ARC
tags: [cocos2d, arc, objective-c, programming, ios]
modified: 7-22-2011
---

This is my very first post for #iDevBlogADay. I had been scheduled to do it earlier this year, but then something happened. I’m not exactly torn up about it. :)

In my last post, I mentioned a new project that we’re attempting to do before we hop back on to Sol Defender. We decided Cocos2D was the right fit for the project, as the game simply works better in 2D. We also really wanted to use a lot of the new features coming in iOS 5, which while technically accessible to Unity through plugin “glue code” is simply quicker and easier to access if you’re working entirely in Objective-C.

Due to the iOS 5 requirement, I specifically wanted to start using ARC (automatic reference counting) as I (and Apple) thinks this is the way to go moving forward regarding Objective-C. If you don’t know about ARC, I suggest you read up on it a bit, but in a nutshell, it reduces (eliminates?) memory leaks by removing the need for calls to retain, release, and autorelease as the compiler will handle inserting these calls for you. Apple even managed to get ARC backwards-compatible with iOS 4, so even if you’re not using any iOS 5 specific features, you may still want to make your next game with ARC.

The thing is, if you try to compile the Cocos2D files with ARC, you’ll be greeted by a nice big list of broke. So how do you get Cocos2D and ARC to play nicely together?

## No ARC for Cocos2D

Unless you want to completely rewrite Cocos2D from the ground up to be ARC-compatible, you’re going to have to disable ARC for the Cocos2D source code. If you’re using one of the Cocos2D templates or have simply dragged the source code into your Xcode project, you’ll need to disable ARC for each of the .m Cocos2D source files by setting the compiler flags for each Cocos2D source file.

But there’s an easier way, and it uses something called cross-project references. For Cocos2D, this is actually quite simple to set up. In your Cocos2D source folder, you’ll find an Xcode project called *cocos2d-ios.xcodeproj*. Simply drag this into your Xcode project, and it’ll show up as a project within your project. You can then go into your project *Build Phases* settings and add the Cocos2D libs to the *Link Binary With Libraries* build phase.



After this, go to your project’s *Build Settings*, set *Always Search User Paths* to YES, and add your Cocos2D source directory to *User Header Search Paths*, and make sure it’s marked *Recursive*.



Now what was the benefit of doing all of this? Well, instead of going through all of the Cocos2D source files and disabling ARC compiling for each one of them, you can simply disable ARC compiling for just the Cocos2D project (and thus the Cocos2D static library it creates) and leave it enabled for the rest of your project. In fact, the Cocos2D project should already have ARC compiling disabled, so you’ll just need to make sure ARC is enabled for your parent project. (Note: While ARC itself isn’t under NDA as it’s a part of LLVM, the new Xcode which integrates ARC settings is, so you’ll have to figure out how to enable ARC for your project yourself. It’s pretty easy though, and you can always poke around the Apple Developer Forums to find what you’re looking for.)

## Some Cocos2D Modifications

If you were to build your project as it is, you’ll notice that it still doesn’t compile, complaining about ARC stuff. What gives? I thought we turned off ARC compiling for Cocos2D? We did. The source files are not being compiled under ARC. What is being compiled under ARC is any header files you include in your ARC-compiled source files (i.e., cocos2d.h, and everything that it includes).

So what do we do? We’ll need to make some modifications to header files, but it’s only a few files. I’ve forked the main Cocos2D git repository and included these changes here, so if you want you can just download that and use the Xcode project in it instead of making these modifications by hand. I’ll try to keep this repo integrated with new versions of Cocos2D.

First, open up *CCDirectorIOS.h*. Find the `CCDirectorFast` interface, and remove the `NSAutoreleasePool` ivar. Switch over to `CCDirectorIOS.m`, and add the following somewhere:

{% highlight objective-c %}
static NSAutoreleasePool *autoreleasePool = nil;
{% endhighlight %}

Then, open up *CCActionManager.h*. Near the top, you’ll see a struct called `tHashElement`. This struct references some Objective-C objects, which is a no-no in ARC. To get around this, you simply want to add some modifiers to tell the compiler that we know what we’re doing and to not worry:

{% highlight objective-c %}
typedef struct _hashElement
{
	struct ccArray	*actions;
	__unsafe_unretained id			target;
	NSUInteger		actionIndex;
	__unsafe_unretained CCAction		*currentAction;
	BOOL			currentActionSalvaged;
	BOOL			paused;
	UT_hash_handle	hh;
} tHashElement;
{% endhighlight %}

And finally, the biggest change of them all. If you go to ccCArray.h, you’ll notice a similar situation to above, a struct with a reference to an Objective-C object, which can be easily solved with `__unsafe_unretained`:

{% highlight objective-c %}
typedef struct ccArray {
	NSUInteger num, max;
	__unsafe_unretained id *arr;
} ccArray;
{% endhighlight %}

But the bigger problem is the rest of the file. There’s a bunch of inline functions which do all sorts of un-ARC-able things. Unfortunately, the only real solution is to move the body of these functions into a new *.m* source file, and leave behind only the function prototypes in the header.

## Retain Knowledge, Not Objects

After you’ve done these few modifications, you should be able to compile and bask in the glory that is ARC! Should you retain that `CCSprite` object? Who knows! You don’t have to worry about it anymore! You can create Cocos2D objects in your ARC code, and the compiler will handle all of the retain/release stuff for you. Meanwhile, the Cocos2D objects handle their own memory management internally. They both work together so that you can write your code faster and cleaner than before.

Note that you can also use this trick with other 3rd party libraries. As you might have noticed in the screens above, I have Objective-Chipmunk (an Objective-C version of the Chipmunk physics library) working with ARC as well, and it only required some minor changes in a single header file.

If you have any questions about this procedure, just post a comment below, and I’ll try to answer any questions to the best of my knowledge.