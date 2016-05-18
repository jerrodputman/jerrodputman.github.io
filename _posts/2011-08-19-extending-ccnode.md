---
title: Extending CCNode
description: "Discussion on how to extend CCNode without actually modifying the Cocos2D source code."
categories: Technical
tags: [Cocos2D, Objective-C, programming, iOS, categories]
published: 8-19-2011
---

If you’re using [Cocos2D][cocos2d], for the most part you’ll be using `CCSprite` and other `CCNode`-derived classes as is. If you need to make extensions to these classes, you simply make a subclass derived from those classes and write only what you need.

But what if you need to extend `CCNode` itself? You could subclass `CCNode`, but then `CCSprite` and other subclasses wouldn’t get the benefits of your subclass. You could modify the `CCNode` source code, but if you update to a future version of Cocos2D, you’re likely to run into merge problems or (worse) lose all of your changes.

This is where we can leverage the fact that Cocos2D is written in Objective-C. The language provides lots of excellent features that allow us to extend a pre-existing class without subclassing.

## Category Extensions

If you’ve been working with Objective-C for very long, you’ve probably come into contact with the concept of categories. In a nutshell, a category allows you to add methods to an existing class, even if you don’t have the source code for that class. Categories are simple enough to use. In your header file, you simply declare the category like so:

{% highlight objc %}
@interface CCNode (MyExtensions)
 
- (void)myExtensionMethod;
 
@end
{% endhighlight %}

In this example, `MyExtensions` is the name of the category. Then, in your implementation file:

{% highlight objc %}
@implementation CCNode (MyExtensions)
 
- (void)myExtensionMethod
{
	// Do something interesting here.
}
 
@end
{% endhighlight %}

So now, on any `CCNode` (or any subclass derived from `CCNode`, including `CCSprite`), you can do something like this:

{% highlight objc %}
CCSprite *aSprite = [CCSprite spriteWithFile:@"test.png"];
[aSprite myExtensionMethod];
{% endhighlight %}

You can also override methods in `CCNode` with a category, but you have to be very careful with this. In fact, Apple says this is “strongly discouraged”. The problem arises when you have two categories trying to override the same method. The method that takes precedence is not defined.

So as you see, categories can be pretty useful, but they do have one particularly annoying limitation: You can’t add member variables to a class through a category. This becomes extremely limiting when you need to add something like per-instance storage of an array or reference. However, there’s another (more advanced) feature of Objective-C we can use to get around this limitation.

## Associative References

There is a set of Objective-C runtime functions that allows you to create associative references. Basically, it allows you to make an association between one object and another, usually to simulate the addition of object instance variables to an existing class without having to modify the original class.

Let’s use an example from [CCKit][cckit] to illustrate how to get this working. In `CCGestureRecognizer`, we need to keep track of all of the gesture recognizers that have been added to a `CCNode`. Before, we would have to go into the `CCNode` code and add an `NSMutableArray` to the class declaration. However, since CCKit is a set of standalone classes, this approach wouldn’t work.

So in the `addGestureRecognizer:` method (which is part of a `CCNode` category called `GestureRecognizerAddtions`), we do something like this:

{% highlight objc %}
// A key to access the associative reference.
static char CCNodeGestureRecognizerAdditionsKey;
 
NSMutableArray *gestureRecognizers = [[NSMutableArray alloc] init];
[gestureRecognizers addObject:gestureRecognizer];
 
objc_setAssociatedObject(self, &amp;CCNodeGestureRecognizerAdditionsKey, gestureRecognizers, OBJC_ASSOCIATION_RETAIN);
{% endhighlight %}

As you can see, we created an `NSMutableArray`, added an object to it, and then called the Objective-C runtime function `objc_setAssociatedObject`. The first parameter is the object you want to associate with (in this case, a `CCNode`), the second is a key with which you’ll access the associated object (the key must be a void pointer, so we create a static variable here for simplicity), the third is the actual object we want to associate, and the last parameter is the association policy. As you can probably guess, the association policy is similar to property attributes. The above is essentially like declaring this:

{% highlight objc %}
@property (retain) NSMutableArray *gestureRecognizers;    // "strong" would replace "retain" in ARC.
{% endhighlight %}

So we’ve set up the association. How do we access the object?

{% highlight objc %}
NSMutableArray *gestureRecognizers = objc_getAssociatedObject(self, &CCNodeGestureRecognizerAdditionsKey);
{% endhighlight %}

And that’s it! We just added a member variable to `CCNode` without actually changing its source code! Additionally, the array will be released when the `CCNode` is deallocated, so it even cleans up after itself.

Of course, you’ll only want to use this in certain situations. But if you need to extend the functionality of `CCNode` by adding member variables, and a subclass won’t work for you, associative references are an excellent way to accomplish what you need.

[cocos2d]: http://www.cocos2d-iphone.org
[cckit]: http://www.github.com/jerrodputman/cckit