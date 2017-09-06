---
layout: post
title:  "If your code always works, you're not doing it right"
date:   2017-09-06 22:04:05 +0000
---

>"How do you not understand why something isn't working if you're the one who built it?"

One of my best friends recently asked me this over dinner when I was explaining the frustrations/exhilarations I was feeling while debugging various programs. She's a writer and an artist, not a programmer, so to her "building a program" might as well have sounded like "building a Lego castle."

I laughed.

If it were that simple, there wouldn't be much demand for programmers, I told her. But most of what we do is debugging--moving code slowly from a state of broken to fixed. And this is necessary because 1. your code doesn't exist in isolation--it interacts with other code and platforms, and 2. even if you understand your own code fairly well, your code is based on other code that you may not understand quite as expertly.

Back when I was working with HTML and CSS on marketing emails and landing pages there was always at least one platform that wouldn't render the code as it was supposed to appear (looking at you, [most versions of Outlook](http://www.verticalresponse.com/blog/its-not-you-its-outlook-the-complete-guide-for-email-marketers/) and [Internet Explorer](http://imgur.com/ZwyPqDF)). 

Let's say, you wanted to center something on your page. Simple, right? Except that invariably some specific version of some browser on some device with some specific aspect ratio would float your element over to the left. And finally when you managed to center what you wanted on THAT platform, another platform suddenly had issues you didn't anticipate.

###This was my favorite kind of work at my old job.


![](http://i.imgur.com/e16qOEj.gif)


Getting code to work, I explained to my friend while munching on a piece of roasted zucchini, is like working on one of those 3D wooden brain teasers. You may recognize the individual pieces and know what the puzzle is supposed to look like when it's completed, but just because you are putting the pieces together doesn't mean that you fully understand why something just worked or didn't. You can get a bunch of the pieces together seemingly correctly, but then there's inevitably some piece that won't fit within the puzzle's current configuration.

And so, you start shifting pieces around again until suddenly, almost magically, it all falls into place.


![](http://www.rectatech.com/media/catalog/product/cache/1/image/9df78eab33525d08d6e5fb8d27136e95/i/m/image_screen_shot_2012-05-15_at_2.31.44_am.png)


Perhaps this sounds daunting if you're the kind of person who just wants things to work already, but it's what makes programming (and specifically debugging) so much fun for me. I love figuring out how to get my code from a state of broken to working. And then I love when I run into the same issue the next time and remember how I solved it (even if I don't remember why it worked).

