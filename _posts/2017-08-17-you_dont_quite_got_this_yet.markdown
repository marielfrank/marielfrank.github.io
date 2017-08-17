---
layout: post
title:  "You don't quite got this yet"
date:   2017-08-17 16:37:36 -0400
---


Here's the story: 

I had finished my Tic-Tac-Toe with AI group project, breezed through the student scraper, and felt pretty confident in my ability to knock out the CLI Data Gem project in a day or two. 

The examples above the instructions included a video walkthrough for "if you don't know where to start." Pshhh, I thought. The focus here is on OO Ruby, which I have been living and breathing for a couple weeks and web scraping, which I used on a [Python project](https://github.com/marielfrank/download-nth-google-image-result) before I even started at Flatiron. Piece. Of. Cake. ***I got this.***

If only I had watched that damn video.

Languages make me happy, so a language-related app seemed like fun. Something along the lines of Google Translate but Rosetta Stone-style (the actual stone, not the language-learning platform) where you could translate into multiple languages at once and then get alternate translations, etc. I set up my project the way we had been learning: 
* config folder for your environment, 
* bin folder for your executable file, 
* and lib folder for all classes and modules. 

I used Bundler to install my gems, set up the outline of my interface in place, and built out my classes. I grabbed all the CSS elements, ids, and classes I'd need to target everything I needed from Google Translate. Smooth sailing.

And that's when Google Translate ~~did everything it was supposed to~~ refused to scrape. I would run a text scrape with Nokogiri/Open-URI and get nil, nothing, nada, zilch. I double-, triple-checked my CSS classes and ids. It felt like some sort of cruel joke. Except it wasn't a joke.

![](http://68.media.tumblr.com/tumblr_m5tz890Mov1rol1w1.gif)

Now, with a little research I discovered that Google Translate does have an API (Google Cloud Translation API) *and* from what I'd heard, APIs were supposed to make life *easier* than scraping. Excellent, hiccup averted...except for two things:

1. I was committing every single change to GitHub and I had no clue how to keep my API key from ending up on GitHub.
2. Google's API does not allow you to get those sweet, sweet alternate translations, i.e., the details that I needed to satisfy the requirement of the project.

Fortunately, another Flatiron student told me about [dotenv](https://github.com/bkeepers/dotenv) which took care of the API key security. But I now had to go a scrape a totally different site with a totally different structure and different language capabilities. I settled on wordreference.com. By the way, at this point, I was already a week and a half into the project--a far cry from the "day or two" I'd imagined.

Once I got my scraper rebuilt for wordreference, my API functionality set up, and got through debugging, I felt pretty set to move on to the next step--getting my project gem-ready.

And that's when I finally decided to watch the video, where I discovered that I had set up the entire program completely wrong. In fact, the ***very first step*** I was supposed to take to set up my program was to run `bundle gem gem_name` [in order to generate THE ENTIRE FILE STRUCTURE](http://bundler.io/v1.15/man/bundle-gem.1.html).

![](http://68.media.tumblr.com/cd154a1f83461eb2450de92d5676c1e8/tumblr_nrce8sR9J31soke6ro1_400.gif)

Yes, it's not actually that big of a deal. I figured out a hacky workaround to push everything I needed into my program and got a bunch of advice from fellow students. I also suggested that Flatiron include that walkthrough video as the very first instruction so that more over-confident newbies like me don't end up blindsided. Needless to say, this project has been a serious lesson in humility. I know I will get to a point when I can actually knock a project like this out in a day or two. But I'm definitely not there yet.
