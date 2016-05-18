---
title: "The Unity/Objective-C Divide"
description: "Achieving greater control for communicating between Unity script and Objective-C using the Mono runtime."
categories: Technical
tags: [Unity, Objective-C, interop, C#, programming, iOS, technical]
published: 1-10-2010
updated: 3-17-2014
---

I love [Unity][unity]! The Unity engine is a fantastic piece of technology, allowing us to quickly develop games without having to worry about a lot of the underlying stuff. However, Unity is essentially an additional layer on top of the iOS SDK, which means that while the iOS SDK may advance with new features, the Unity engine is generally a step behind with adding these features in a manner that can be easily accessed through script.

Enter Objective-C: This is the language that iOS apps are usually written in. Unity provides some support for calling native Objective-C code from Unity script, but generally only in one direction. This can be pretty limiting especially if you’re trying to use a lot of the fancy features in newer iOS versions. Sure, you can pass around information using the `PlayerPrefs` trick, but this has the problem of not being instantaneous, and it forces the app to constantly poll for new commands, which isn’t a good idea performance-wise.

What follows is a method for immediately calling Objective-C code from Unity script and also vice versa! That’s right, two-way communication between Objective-C and Unity script that’s instantaneous.

## Unity to Objective-C

First, since the release of Unity 4, all Unity iOS licenses have access to a quick and easy way of calling Objective-C code from Unity script. In C#, just do the following:

{% highlight csharp %}
[System.Runtime.InteropServices.DllImport("__Internal")]
extern static public int AwesomeFunction(int awesomeParameter);
{% endhighlight %}

Then, in a C/C++/Objective-C file somewhere in your Unity-built Xcode project, do the following:

{% highlight cpp %}
int AwesomeFunction(int awesomeParameter)
{
   // My awesome code goes here.
 
  return somethingAwesome;
}
{% endhighlight %}

You may have to wrap your function prototypes in

{% highlight cpp %}
extern "C" { ... }
{% endhighlight %}

if you’re using C++/Objective-C++ (or your code is in a `.mm` or `.cpp` file) due to name mangling.

## Unity to Objective-C (The PlayerPrefs Trick)

Previously, the above method was only available for Unity iOS Pro licensees. Since Unity 4, that is no longer the case, but I am leaving this section in the post for reference.

The `PlayerPrefs` trick you may already be familiar with involves sending a command to Objective-C from Unity script like this:

{% highlight csharp %}
PlayerPrefs.SetString("Commands", String.Format("AwesomeCommand|{0}|{1}", awesome1, awesome2));
{% endhighlight %}

Or something similar. You’ll still be sending commands this way. However, in Objective-C, you probably have an `NSTimer` that is constantly polling to see if the “Commands” key in `NSUserDefaults` has a string in it. You might even have some fancy queueing system set up so that you can call multiple commands in a row without them overwriting each other. The issue, though, is that if you need a result back from one of these commands, you can’t get it instantaneously. You have to set up some kind of polling system in Unity script to wait for a return result to show up in some other `PlayerPrefs` key. Messy.

But there’s a better way. Let me introduce you to *Key-Value Observing*. The iOS SDK provides a method for setting up “observers” for certain things currently going on in the system. It just so happens that one of the things that you can “observe” is `NSUserDefaults`. That’s our ticket!

Somewhere in your Objective-C code (where you might normally set up the `NSTimer` for polling the `NSUserDefaults`), place the following code:

{% highlight objc %}
[[NSUserDefaults standardUserDefaults] addObserver:self forKeyPath:@"Command" options:0 context:nil];
{% endhighlight %}

Then, create a method like the following:

{% highlight objc %}
- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary *)change context:(void *)context
{
   // Command parsing code goes here.
}
{% endhighlight %}

The `observeValueForKeyPath:ofObject:change:context:` method is essentially the function you had before that parsed the commands from `PlayerPrefs`/`NSUserDefaults`. What the `addObserver:forKeyPath:options:context:` method does is set self to be an observer of `NSUserDefaults`, meaning that every time you set the `PlayerPrefs` in Unity script, the `observeValueForKeyPath:` method gets called immediately!

What this now allows you to do is to set a result key in Objective-C and immediately access it in Unity script. Here’s an example:

{% highlight objc %}
// Objective-C code
if([[[NSUserDefaults standardUserDefaults] getObjectForKey:@"Command"] isEqualToString:@"DoSomething")
{
   // Return a result by setting the key here.
   [[NSUserDefaults standardUserDefaults] setInteger:1 forKey:@"Result"];
}
{% endhighlight %}

{% highlight csharp %}
// Unity C# code
int CallDoSomething()
{
   PlayerPrefs.SetString("Command", "DoSomething");
   // Before, doing something like this would be very bad!
   return PlayerPrefs.GetInt("Result");
}
{% endhighlight %}

As you can see, you can get immediate results from the Objective-C code! Hooray!

## Objective-C to Unity (The Mono Runtime Trick)

*NOTE: Unity provides a C function called UnitySendMessage that you can use to send messages back to your scripts. However, it’s not nearly as robust as this method since it waits until the next frame to execute and you can’t call static methods since you have to supply the function with a GameObject name. However, if you don’t need the extra functionality, I recommend you use that instead.*

There are many instances where you need to capture an event in Objective-C and then pass this off immediately to your Unity scripts so that you can update your game. While using `NSUserDefaults` to send information back to `PlayerPrefs` is certainly doable, a polling system would need to be set up in Unity script and it would never be instantaneous.

To get this working, you need to know a little bit about the underlying Unity frameworks. Unity uses a framework called [Mono][mono], which is an open-source cross-platform implementation of .NET. When you use something in the `System` namespace or `String.Format`, you’re actually using .NET libraries (or rather, Mono implementations of .NET libraries), and not something Unity-specific. While the actual invocation of the Mono runtime isn’t visible from inside the Xcode project, that doesn’t mean we can’t still have access to the Mono functions.

You can do amazing things with C. You can also do terrible things with C. That’s why it’s preferred by so many programmers, as data and memory in C is completely malleable. You can do almost anything with it. Even though we don’t have access to the Mono header files in the Unity Xcode project, by doing some handy Internet searches, we can get the function prototypes of all of the Mono functions we’ll need to get what we want.

In addition, many Mono functions require or return special MonoTypes. This would be a problem in C#, but in C a void pointer cures all! For our purposes, here are all of the MonoTypes that we need:

{% highlight cpp %}
typedef void* MonoDomain;
typedef void* MonoAssembly;
typedef void* MonoImage;
typedef void* MonoClass;
typedef void* MonoObject;
typedef void* MonoMethodDesc;
typedef void* MonoMethod;
typedef void* gpointer;
typedef int gboolean;
{% endhighlight %}

To be honest, you don’t even really have to set up the `typedef`s, but it makes the function prototypes look cleaner and more readable. Remember, since pointers simply point to memory, they don’t really need a type.

Now for the function prototypes that we’ll need. The following is the bare minimum of functions needed to accomplish calling Unity script code from Objective-C. (Remember: You may need to wrap these in `extern “C” { … }` if you’re using C++/Objective-C++ due to name mangling.)

{% highlight cpp %}
MonoDomain *mono_domain_get();
MonoAssembly *mono_domain_assembly_open(MonoDomain *domain, const char *assemblyName);
MonoImage *mono_assembly_get_image(MonoAssembly *assembly);
MonoMethodDesc *mono_method_desc_new(const char *methodString, gboolean useNamespace);
MonoMethodDesc *mono_method_desc_free(MonoMethodDesc *desc);
MonoMethod *mono_method_desc_search_in_image(MonoMethodDesc *methodDesc, MonoImage *image);
MonoObject *mono_runtime_invoke(MonoMethod *method, void *obj, void **params, MonoObject **exc);
gpointer mono_object_unbox(MonoObject *obj);
{% endhighlight %}

Here’s where we need to explain things: We call Unity script functions by using the `mono_runtime_invoke function`. However, before we can call this, we need to get the `MonoMethod` (essentially, the pointer to the Unity script function we want to call) from the assembly file that was compiled by Unity when you hit “Build & Run”. So how do we get that?

First things first, we need to get the domain where all of the Unity scripts are running. In the vaguest of terms, a domain is kind of a box where scripts are run. So we need to grab the Mono domain first:

{% highlight cpp %}
MonoDomain *domain = mono_domain_get();
{% endhighlight %}

Simple enough. We now have the location where all of the Unity scripts are being run. Now to get the `MonoMethod`, we first need to get a reference to the assembly that contains the function. In your Xcode project, if you look in the Libraries group, you’ll see a bunch of *dll.s* files. These files get compiled by Xcode into DLL files that get placed into your app’s *Data* folder. If you don’t believe me, take your built Unity app, *Show Package Contents* on it, and then browse around.

Knowing the folder structure of the built application is actually important to this process, as we’re going to be directly referencing these DLLs for the next step. We need to get the assembly itself so we can read through it and find our functions. Here’s how to do that:

{% highlight cpp %}
NSString *assemblyPath = [[[NSBundle mainBundle] bundlePath]
   stringByAppendingPathComponent:@"Data/Assembly - CSharp.dll"]
MonoAssembly *assembly = mono_domain_assembly_open(domain, path.UTF8String);
MonoImage *image = mono_assembly_get_image(assembly);
{% endhighlight %}

You’ll notice we put in the path of the built DLL file from the application’s main bundle. Note that the assembly you’re looking for might be different for the class method you’re trying to access. To find out the correct assembly, highlight the script in Unity and go into *Debug* mode in the *Inspector*. The *Assembly Identifier* should tell you the name of the assembly. We then get the assembly from our domain. The `MonoImage` is something we’ll need for the next step.

We have our assembly now, and we can start accessing the functions inside it. To do this, we need to tell Mono what functions we’re looking for. (For ease of use, I’m only going to be looking for static methods in classes without a namespace. If you want to access specific objects or call non-static methods, I suggest you read up on the official documentation on [Embedding Mono][embedding-mono].)

{% highlight cpp %}
MonoMethodDesc *desc = mono_method_desc_new("AwesomeClass:AwesomeFunction()", FALSE);
MonoMethod *method = mono_method_desc_search_in_image(desc, image);
mono_method_desc_free(desc);
{% endhighlight %}

Notice that the `MonoMethodDesc` (literally, *MonoMethod Description*) is created by giving `mono_method_desc_new` a string that contains the method that we want to call. Remember to put the class name before the function and follow it with a colon, not a period. If you want to access a method with parameters, do something like this:

{% highlight cpp %}
"AwesomeClass:AwesomerFunction(int,int,int)"
{% endhighlight %}

Formatting is very important. There shouldn’t be any spaces between the method parameters. After getting the description, we then search the assembly image for the method. Doing all of this searching could impact performance in your app, so it’s recommended that you do all of this in an initialization function and store the pointers to the various `MonoMethod`s you want to use. After we have the method pointer, we free the `MonoMethodDesc` that was created.

Now you have your `MonoMethod`! And you say you want to call it! We can do that very easily now:

{% highlight cpp %}
mono_runtime_invoke(method, NULL, NULL, NULL);
{% endhighlight %}

See? Very simple. If your method had some parameters in it, you need to pass the arguments as a void pointer array to the third parameter of `mono_runtime_invoke`, like so:

{% highlight cpp %}
int param1 = 1;
int param2 = 27;
void *args[] = { &param1, &param2 };
 
mono_runtime_invoke(method, NULL, args, NULL);
{% endhighlight %}

We are essentially passing the arguments by their address via way of an array.

If your Unity method returns a value (such as an integer), you can retrieve that value by doing something like this:

{% highlight cpp %}
MonoObject *result = mono_runtime_invoke(method, NULL, args, NULL);
int int_result = *(int *)mono_object_unbox(result);
{% endhighlight %}

And that’s it! There are a few caveats to using this method of invoking Unity script methods: Firstly, it’s slow. It would not be recommended to call these many times per frame or even once every frame. It’s primarily good for callbacks that occur every now and then (like for *GameKit* connection results, etc). Secondly, you’re putting a lot of trust in the Mono and Unity developers to not change things around too drastically. If you upgrade Unity and your code suddenly stops working, you’ll need to downgrade to the previous version of Unity or muck around until you can find out what was changed.

## Conclusion

Finding all of this out was a long weekend project for me, and it has definitely been worth it. Functionality that I simply didn’t think was possible without official Unity support is now immediately available, and I am now able to bounce back and forth between Unity script and Objective-C to get access to whatever I need. There are performance issues to consider, but on the whole the flexibility outweighs the potential performance pitfalls. It just requires you to properly plan your app with these things in mind.

It should be added, using the Objective-C to Unity callback functionality is not for the faint of heart, and certainly not for those who aren’t well-versed in the art of programming. Just be sure to design your games around what you **can** do, not around what you **wish** you could do.

I hope you’ve found this both educational and useful and that you’ll consider contributing your own findings to the Unity community.

[unity]: http://www.unity3d.com
[mono]: http://www.mono-project.com
[embedding-mono]: http://www.mono-project.com/Embedding_Mono