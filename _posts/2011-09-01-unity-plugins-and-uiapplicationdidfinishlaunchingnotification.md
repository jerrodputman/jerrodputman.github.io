---
title: Initializing iOS Unity Plugins
description: "Easily initialize Unity iOS plugins at app startup with UIApplicationDidFinishLaunchingNotification."
categories: Technical
tags: [Unity, plugins, C#, Objective-C, iOS, programming]
published: 9-11-2011
---

So you’re writing your Unity plugin to do some cool native functionality, but you really need it to do some work right when the app launches. There are usually two ways to solve this problem: 1) Create an “init” function that you call from Unity script when your first scene is loaded, or worse 2) modify `application:didFinishLaunchingWithOptions:` directly. Technically, there’s a third option too, involving creating a category of the `AppController` delegate and “overriding” `application:didFinishLaunchingWithOptions:`. The OpenFeint Unity plugin uses this method, actually, but as I mentioned in a [previous post][extending-ccnode], this has some serious drawbacks, especially if you want to use the technique more than once (and yes, I realize I’m the one who originally wrote that plugin… I didn’t know!).

I’d like to introduce you to the `UIApplicationDidFinishLaunchingNotification` key, something which has become a good friend of mine.

It always bugged me that the `UIApplicationDidFinishLaunchingNotification` key existed. I couldn’t possibly think of a use for it. In my mind, the first place that any application code could ever run was in `application:didFinishLaunchingWithOptions:` (or its precursor, `applicationDidFinishLaunching:`), and so the key was not necessary. I assumed it was just some thing required by the OS that we weren’t really supposed to use.

But one day, I decided to actually investigate the uses of this key instead of basing my assumptions on pure speculation. I found the `NSObject` class method `load`. Suddenly, the key started to make sense.

The documentation for load says that the method is “invoked whenever a class or category is added to the Objective-C runtime; implement this method to perform class-specific behavior upon loading.” Essentially, what this means is that any code inside your class’s load class method will be executed before `application:didFinishLaunchingWithOptions:`. If you want to write any startup code, this seems like it would be the place to do it. But there is a caveat: Other classes may not have loaded themselves yet, so it’s not safe to do too much in this method.

Instead, you should add your class as an observer of the `UIApplicationDidFinishLaunchingNotification` key with an action method to do any initialization you want. This will allow all classes to be fully loaded, but still allow for initialization code to be run without having to have either an initialization function in Unity script or by modifying `AppController`’s `application:didFinishLaunchingWithOptions:`.

Here’s an example:

{% highlight objc %}
@interface MyCoolPlugin : NSObject
 
// Class stuff.
 
@end
{% endhighlight %}

{% highlight objc %}
static MyCoolPlugin *_sharedInstance = nil;
 
@implementation MyCoolPlugin
 
+ (void)load
{
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(createMyCoolPlugin:) name:UIApplicationDidFinishLaunchingNotification object:nil];
}
 
+ (void)createMyCoolPlugin:(NSNotification *)notification
{
    // This code will be called immediately after application:didFinishLaunchingWithOptions:.
    // You could use this to create an instance of your plugin class, like so:
    _sharedInstance = [[MyCoolPlugin alloc] init];
}
 
// Your plugin methods.
 
@end
 
// C functions to access your plugin from Unity script.
{% endhighlight %}

Not only can you use `UIApplicationDidFinishLaunchingNotification`, but you can also observe keys like `UIApplicationDidEnterBackgroundNotification`. Essentially, you can plug into most of `UIApplicationDelegate`‘s methods without having to modify Unity’s `AppController`. And naturally, if you’re creating standalone Objective-C libraries, this method is essentially required unless you want your users to modify their app delegate.