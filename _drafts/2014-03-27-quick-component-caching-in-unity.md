---
title: "Quick Component Caching in Unity"
description: "Avoid premature optimization with this quick tip on how to quickly cache Unity components with only two lines of code."
categories: Technical
tags: [Unity, C#, programming, tips, technical]
published: 3-27-2014
---

There is [a famous quote from Donald Knuth][knuth-quote] about optimization that says:

> We *should* forget about small efficiencies, say about 97% of the time: **premature optimization is the root of all evil**. Yet we should not pass up our opportunities in that critical 3%.

If you've done any programming in [Unity][unity] for long, one common optimization technique you learn about is caching components. For example, instead of doing this every time:

{% highlight c# %}
GetComponent<MyScript>().DoAThing();
{% endhighlight %}

it's much more efficient to cache the component in Awake or Start:

{% highlight c# %}
private MyScript cachedComponent;
void Awake()
{
	cachedComponent = GetComponent<MyScript>();
}
{% endhighlight %}

and then use the cached reference exclusively:

{% highlight c# %}
cachedComponent.DoAThing();
{% endhighlight %}

Obviously, this makes a lot of sense, and for all of your custom components, you should probably be doing this anyways.

However, what about Unity's built-in accessors such as `transform`, `audio`, or `rigidbody`? Well, according to [Unity's own scripting optimization documentation][unity-optimization], these properties simply call `GetComponent` for you. The doc recommends caching frequently used components (especially `Transform` components) if you're going to be using them frequently (once a frame or more).

Having this knowledge, a lot of us spend a lot of time writing code like this for each of our scripts:

{% highlight c# %}
private Transform cachedTransform;
private Rigidbody cachedRigidbody;
private AudioSource cachedAudio;

void Awake()
{
	cachedTransform = GetComponent<Transform>();
	cachedRigidbody = GetComponent<Rigidbody>();
	cachedAudio = GetComponent<AudioSource>();
}
{% endhighlight %}

But that's technically premature optimization. It may not actually make much of a difference wasting all of this time writing this for each and every script.

The alternative is to simply wait until you're at the optimization step of development. But then, you have another problem. Not only do you now have to write the boilerplate code above, but you also have to go through and replace all instances of `transform`, `rigidbody`, and `audio` in your script, which has the potential to be a bug-producing operation.

There's a simple way to get around this problem, and it uses the C# `new` keyword.

{% highlight c# %}
private new Transform transform;
private new Rigidbody rigidbody;
private new AudioSource audio;

void Awake()
{
	transform = GetComponent<Transform>();
	rigidbody = GetComponent<Rigidbody>();
	audio = GetComponent<AudioSource>();
}
{% endhighlight %}

Essentially, what you're doing is *hiding* Unity's built-in properties in favor of your own cached member variables. Now, any place in your script where you used `transform` will now use your cached version. If you wanted to expose the cached variable to the outside world, you could even do the following:

{% highlight c# %}
[System.NonSerialized]
public new Transform transform;
{% endhighlight %}

The `NonSerialized` attribute is added because we don't want to serialize the cached version.

And that's it! You're done! You've just performed some simple optimization on your script, and it only took two lines per component. You avoided premature optimization *and* you avoided massive refactoring of your code. That's what I call a win/win!

If you're looking for additional Unity tips, I suggest you take a look at [Unity Patterns][unity-patterns]. It's a great site with a lot of awesome, useful tips (and some great lightweight plugins too).

[knuth-quote]: http://en.wikipedia.org/wiki/Program_optimization#Quotes
[unity]: http://www.unity3d.com
[unity-optimization]: http://docs.unity3d.com/410/Documentation/ScriptReference/index.Performance_Optimization.html
[unity-patterns]: http://unitypatterns.com