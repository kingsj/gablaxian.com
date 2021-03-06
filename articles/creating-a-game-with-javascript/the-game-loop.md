---
layout: article-game-dev
title: The Game Loop
disqus: true
---

Coming from a Web Development background, you may be forgiven for expecting a game to be written in a kind of event driven fashion. The user clicks a thing/presses a key and then stuff happens. Reactionary, if you like. And you know what? In the grand sphere of games, there is absolutely no reason that can’t be the case. In fact, word games like Letterpress, Wordfeud and Words for Friends work on this mechanic. Those games you play on your consoles, however, do not. Oh no. Games are, in essence, interactive movies, but one that you can influence. And a movie is simply a sequence of frames. The Game Loop is, then, the processes a game goes through each time it creates this frame.

Our Zelda game will follow this simplified sequence each time it’s loaded:

- Initialisation
    - Load assets
    - Pre-calculations
- Game Loop
	- Clear the screen
	- Retrieve Player Input
	- Process AI & Logic
	- Draw graphics
	- Update the screen

The loop continues until the user breaks out of it by either pausing or quitting. As we’ll learn throughout the development of this game, however, since we’re inside a browser, JS allows–and even forces–us to do things a bit differently.

In PC & Console games, this loop is usually a while(true) loop and it will run indefinitely and as fast as the processor will allow. We can do something similar with Javascript in the browser. There are ways to get the framerate as high as the browser will allow, as documented nicely [here](http://www.chandlerprall.com/2011/06/beating-60fps-in-javascript/). There are pros and cons to each method, but honestly, games are typically complicated enough that running above 60fps is hard to maintain, and beyond 60fps our eyes can no longer keep up, so any frames beyond that are effectively wasted, making 60fps the sweet spot. And, luckily, Javascript gives us a few nicer options to achieve a loop at that speed. Whether or not we’ll actually be _able_ to run our game at that speed is another matter.

So, let’s get going! At the moment, our [game](https://github.com/gablaxian/super-js-adventure) is pretty simple. One HTML file with a basic HTML structure, a few styles and a canvas element:

{% highlight html %}

<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Super JS Adventure!</title>
    
    <style type="text/css" media="screen">
        * { margin: 0; padding: 0 }
        #container { width: 256px; height: 224px; border: 1px solid #999; margin: 20px auto 0 auto; }
    </style>
    
</head>

<body>
    
    <div id="container">

        <canvas id="super-js-adventure" width="256" height="224"></canvas>

    </div>
    
    <script src="js/main.js"></script>
</body>
</html>

{% endhighlight %}

The canvas element may look a bit small, but that’s the native resolution of Zelda: A Link to the Past. Scary, huh? Obviously, it was scaled up when shown on TVs, and we’ll get to that later.

The JS file is even more simple:

{% highlight js %}

var canvas  = document.getElementById('super-js-adventure'),
    ctx     = canvas.getContext('2d'),
    width   = 256,
    height  = 224;

{% endhighlight %}

We first get hold of the canvas element which just sets up a drawing surface, then get its 'context', the actual surface we'll be manipulating. I've also stored the inital size of the canvas just in case.

So now we get some sort of loop going. For now, I’ll be emulating the sort of the loop you’d see in a basic PC Game. It’ll likely evolve over time.

## Javascript Timers

Historically, Javascript has provided two functions to use for looping: `setInterval()` and `setTimeout()`. These are actually timers, as opposed to loops. You can pass `setInterval()` a function and a time, x, in milliseconds and it will fire that function every x ms:

{% highlight js %}

setInterval( function() { // I'm looping! }, 16 );

{% endhighlight %}

This runs the anonymous function every 16 seconds, which is around 60fps (16.6 recurring, if you must know). Or, you could use `setTimeout()`, albeit a little differently, which runs a function after x ms:

{% highlight js %}

setTimeout(function(){
    /* Some long block of code... */
    setTimeout(arguments.callee, 16);
  }, 16);

{% endhighlight %}

However, much has been written about the downsides of both these techniques. John Resig, of jQuery fame, wrote a technical but succint post [here](http://ejohn.org/blog/how-javascript-timers-work/). Thankfully, all the latest browsers support a new function: **requestAnimationFrame()**.
It’s preferred because in essence it tells the browser that something wants animating, which is good because browsers are already drawing to the screen, so we’re sort of piggy-backing off that. With the timers, the browser was unaware that an animation was taking place.
It also runs _up to_ 60fps, but no faster. And since the browser is aware of its existence, if we minimise the browser, it can slow down the loop so that your CPU isn’t working unnecessarily, making it the most efficient method.

So, let’s get that added in along with functions for initialisation and the main loop:

{% highlight js %}

function init() {
    // Initialise the game!
}

function main() {
    // Here's where we handle all the input, logic and drawing to the screen per frame.

    // call itself by requesting the next animation frame, and so begin the endless loop
    requestAnimationFrame(main);
}

// Initialise
init();

// Start the loop!
requestAnimationFrame(main);

{% endhighlight %}

Pretty simple, eh? We don't have much to show for it, but it you fire that up in your browser, you’ll have a canvas ready to be drawn to, and a loop running at 60fps. Good job! Have a biscuit.

Join me next time when we deal with drawing to the screen!

<div class="pagination clearfix">
    <a class="left" href="/articles/creating-a-game-with-javascript/introduction.html">&larr; Introduction</a>
    <a class="right" href="/articles/creating-a-game-with-javascript/drawing-to-the-screen.html">Drawing to the screen &rarr;</a>
</div>
