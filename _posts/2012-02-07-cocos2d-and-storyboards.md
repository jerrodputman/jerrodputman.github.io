---
title: Cocos2D and Storyboards
description: "How to easily use iOS Storyboards in a Cocos2D project."
categories: Technical
tags: [Cocos2D, storyboards, iOS, programming]
published: 2-7-2012
---

Storyboarding in iOS is really neat. It allows you to lay out nearly your entire interface visually, and gives you some neat features that simply make your life as a developer easier. Since our next game (an RPG) is going to be extremely UI-heavy, naturally we wanted to use Storyboarding to create our interface. The problem comes when it’s time to fire up Cocos2D and display our battle scenes.

However, with [Cocos2D 2.0 (the OpenGL ES 2.0 version)][cocos2d-2.0] and iOS 5′s view controller containment functionality, integrating Cocos2D in a Storyboard is actually very trivial.

## Containing Cocos2D

Let’s start by creating a new class called `CocosViewController`, which derives from `UIViewController`. It should also conform to the `CCDirectorDelegate` protocol.

{% highlight objc %}
#import "cocos2d.h"
 
@interface CocosViewController : UIViewController <CCDirectorDelegate>
 
@end
{% endhighlight %}

So far, so good. Now, there’s something important to note about Cocos2D 2.0′s director, especially if you’re used to 1.x: The 2.0 director actually derives from `UIViewController`! This makes it incredibly easy to add a Cocos2D view to our hierarchy.

Over in the implementation file, you’ll want to set up your viewDidLoad method like so:

{% highlight objc %}
- (void)viewDidLoad
{
    [super viewDidLoad];
 
    CCDirector *director = [CCDirector sharedDirector];
 
    if([director isViewLoaded] == NO)
    {
        // Create the OpenGL view that Cocos2D will render to.
        CCGLView *glView = [CCGLView viewWithFrame:[[[UIApplication sharedApplication] keyWindow] bounds]
                                       pixelFormat:kEAGLColorFormatRGB565
                                       depthFormat:0
                                preserveBackbuffer:NO
                                        sharegroup:nil
                                     multiSampling:NO
                                   numberOfSamples:0];
 
        // Assign the view to the director.
        director.view = glView;
 
        // Initialize other director settings.
        [director setAnimationInterval:1.0f/60.0f];
        [director enableRetinaDisplay:YES];
    }
 
    // Set the view controller as the director's delegate, so we can respond to certain events.
    director.delegate = self;
 
    // Add the director as a child view controller of this view controller.
    [self addChildViewController:director];
 
    // Add the director's OpenGL view as a subview so we can see it.
    [self.view addSubview:director.view];
    [self.view sendSubviewToBack:director.view];
 
    // Finish up our view controller containment responsibilities.
    [director didMoveToParentViewController:self];
 
    // Run whatever scene we'd like to run here.
    if(director.runningScene)
        [director replaceScene:[MyScene scene]];
    else
        [director runWithScene:[MyScene scene]];
}
{% endhighlight %}

And so that we’re nice and polite programmers, we’ll clean up after ourselves:

{% highlight objc %}
- (void)viewDidUnload
{
    [super viewDidUnload];
 
    [[CCDirector sharedDirector] setDelegate:nil];
}
{% endhighlight %}

And that is quite literally all of the code that you need to write to get the basics up and running. Let me explain a little bit about what’s going on.

In `viewDidLoad`, we first check to see if the director’s view has been loaded. Remember that the Cocos2D director is now simply a subclass of `UIViewController`, so we can use methods like `isViewLoaded` to get information that we need. If the view hasn’t been loaded, that means we haven’t created an OpenGL view for the director yet, so we do that if necessary.

After that, we do the real magic, which uses iOS 5′s view controller containment. We first add the director as a child view controller. Doing this means that the director’s `viewWillAppear:`, `viewWillDisappear:`, et al will be called properly, and in Cocos2D 2.0, that means that the director will automatically pause and unpause itself when the view controller disappears or appears. Awesome!

We then add the director’s view (the OpenGL view) to our view controller’s view hierarchy and send it to the back. Sending it to the back allows us to place buttons and other UIKit stuff on top of the OpenGL view in *Interface Builder*. Finally, we need to call the director’s `didMoveToParentViewController:` method, as required for view controller containment. We then run our scene, and that’s it!

## Adding Cocos2D to a Storyboard

Here we have a perfectly normal Storyboard, with a navigation controller and a view controller that has a simple button.

![Setting up a Storyboard](/images/2012-02-07/1.png)

Now let’s drag a new view controller onto the canvas,

![Creating a new view controller](/images/2012-02-07/2.png)

and assign its class to the one we just created.

![Assigning a class to the view controller](/images/2012-02-07/3.png)

Set up a segue between the button and our new view controller. Set the segue type to “Push” for now.

![Setting up a segue between view controllers](/images/2012-02-07/4.png)

And now, just build and run! You should be able to press the button to load your Cocos2D scene, press the back button to stop it, and repeat the process over and over again! You can even add a button to the `CocosViewController` if you’d like.

## Some Notes

As you may have noticed, Cocos2D’s initialization happens whenever `CocosViewController` is first pushed onto the navigation stack (aka, lazy loading). You may prefer to initialize Cocos2D right at launch to minimize the initial delay.

Also note that if you want to have Cocos2D in the background and have a navigation controller on top (so that you can have a navigation structure for your pause menu, for example), you’ll need to add another navigation controller as a child view controller. View controller containment isn’t really supported in *Interface Builder*, sadly, so if you do this, you’ll probably want to create another storyboard file and load it as your child view controller.

Additionally, there may be some gotchas with this code that I just don’t know about, as it’s only had minimal testing. So let me know if you run into any troubles, and I’ll try to keep the post updated.

## GitHub Repo

If you don't want to reinvent the wheel, I've uploaded [a ready-to-use class][ccviewcontroller] for your enjoyment to [GitHub][github].

[ccviewcontroller]: http://www.github.com/jerrodputman/ccviewcontroller
[github]: http://www.github.com
[cocos2d-2.0]: https://github.com/cocos2d/cocos2d-iphone/tree/gles20