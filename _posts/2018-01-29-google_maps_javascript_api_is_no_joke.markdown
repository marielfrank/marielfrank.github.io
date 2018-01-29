---
layout: post
title:      "Google Maps Javascript API is no joke"
date:       2018-01-29 23:27:39 +0000
permalink:  google_maps_javascript_api_is_no_joke
---

**or: How I Learned to Stop Worrying and Love the Debugger**

When it came to my [Rails app with jQuery](https://github.com/marielfrank/where2p), the requirements were just not enough for me. 

I added a little jQuery front-end including a preview option for my restrooms, created some JS objects without entirely seeing the added benefit of doing such since they just disappear when you refresh the page (more on this later), and committed my changes via git so much and in such detail, you could practically use the messages as a how-to guide. But it's only after I checked off all the boxes on my specs that the real challenge began for me.

Because when you have an app that supposedly helps you find the closest restroom, doesn't it seem like it's totally useless unless you can actually **FIND** the closest restroom? *My* app would **NOT** be a piece of crap. This app needed to sparkle.

![](http://www.reactiongifs.com/r/2013/01/fantastic.gif)

This is why I embarked on a Google Maps Javascript API journey into the unknown and added over a week of extra work for myself. 

I began by sorting through what I actually needed to do. Google Maps has like 20 different APIs with different utilities, so it took me awhile to figure out that what I really wanted was the [Google Maps Javascript API](https://developers.google.com/maps/documentation/javascript/) with [Distance Matrix](https://developers.google.com/maps/documentation/javascript/distancematrix) and [Directions](https://developers.google.com/maps/documentation/javascript/directions) enabled. Fortunately, I wasn't a stranger to the Google API world, so I knew how to set up a key and enable APIs (phew!). But holy moly I learned a lot. 

**Note to self #1: JS has built-in functionality to get your device's location**

I knew that I wanted to 1. get the user's location and 2. save the user's location to Rails. (Originally, I'd considered running a loop to constantly check and update the user's location, but I decided that was something I could do later.) I was thrilled when I came across a way to accomplish the first step using simple vanilla JS:

```
navigator.geolocation.getCurrentPosition(function (pos) { 
     // do something with the latitude and longitude here
};
```

**Note to self #2: Rails strong params with SQLite3 does NOT allow for hashes as values** 

The second step just required a `patch` request to update my `current_user` with the current position. I figured I would store the latitude and longitude as a hash in a user attribute called `position`. Simple, right?

Well, sort of. I got an error throw at me by my user controller's strong params about my :current_position parameter being unpermitted. I spent about an hour debugging, trying to find the typo I knew had to be hiding somewhere before I finally threw in the towel and asked for help.

Thanks to a few fellow Flatironers on slack, I discovered that I did not have a typo lurking, but rather I'd been trying to save something that Rails strong params with SQLite3 simply would't permit: a hash. I quickly split this into two attributes (`current_lat` and `current_lng`) and everything proceeded smoothly.

**Note to self #3: JS objects are actually amazingly useful**

Now that I had my user's location, I needed to find out how far away (and how long it would take the user to get to) the restrooms in my database and then display the restrooms in order by distance. So I set everything up and got this response object from Google:

![](https://i.imgur.com/XQv548T.png)

Oh man, I thought, there must be *some* way to keep track of the restrooms in an orderly fashion with ids that match the database, latitude, and longitude...

And this is why I want to give a shout out to the Distance Matrix for helping me truly understand the value of JS objects. THIS is where JS objects really shine. 

In my `done()` function I added:
```
restrooms.forEach(function (restroom, idx, arr) {
     restroom.distance = response['rows'][0]['elements'][idx]['distance']['text'];
     restroom.duration = response['rows'][0]['elements'][idx]['duration']['text'];
     saveRestroom(restroom);
});
```

**Note to self #4: AJAX does not play well with forEach()**

AJAX is pretty awesome, but it can get a little out of hand when you want to perform the same function on multiple elements. My database started throwing a lot of errors as it got bombarded with AJAX `patch` requests. I quickly discovered with a little Googling that asynchronous requests aren't a good match when you want to use `forEach()`. And there were a lot of suggestions, including [JS Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) and recursively sending the request for a certain number of times if it failed. However, the simplest path by far here was to just change the request's `async` value to `false`.

With `async: false`, the `patch` requests flew seamlessly, one after another.

**Note to self #5: When JS says something is undefined, you probably forgot to call/define it on load before it's getting called in a function**

Nothing beats the utter frustration of this JS situation:

You have a function that calls another function. The first function throws an error at you like `Type error: restroom is undefined`. So you throw  `debugger` in that function to see what's going on. When the debugger hits, you type `restroom` in the console. And you get back the restroom object you wanted. Huh?

It's only after over an hour of breaking down your code that you realize that there is this possibility you made that super-obvious noob mistake that your instructors warned you about: you didn't add function #2 to your on load function. Which means that JS defines everything in function #2 BEFORE your page has loaded, at which point your JS is like, "`restroom`? That's not a thing I know about."

Thankfully, the next time it came up I caught it faster.
