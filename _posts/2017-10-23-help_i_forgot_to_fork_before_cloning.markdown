---
layout: post
title:      "Help! I forgot to fork before cloning!"
date:       2017-10-23 18:11:19 -0400
permalink:  help_i_forgot_to_fork_before_cloning
---

**Or how to change your `git remote origin` in three simple steps**

Ever accidentally cloned a GitHub repository you don't have `push` access to?

I have, more times than I care to admit.


**The correct order of operations that my brain somehow occasionally fails to recall:**

***FORK*** the repository you want on GitHub like so:

![](https://i.imgur.com/F9xgFiw.png)

![](https://i.imgur.com/4ajwCzA.png)

Then clone the repository from there:

`$ git clone [repo origin path]`

Then get to work on your code.

However, I am amazing at skipping one very crucial step: that whole forking thing. I like to think it's because I'm so excited to dig into the code.


![](http://ericsteinborn.com/github-for-cats/img/forking.gif)

*Forks are important. Just ask this cat.*


**`git` commands to the rescue**

If you suddenly realize (when you try pushing your code back upstream) that you cloned off the wrong `git remote origin` respository, don't panic. Here's what you do:

1. Fork your accidental origin to your own GitHub path exactly as you should have at the beginning
2. Copy the url you ***should*** have cloned from in the first place:

![](https://i.imgur.com/LIKaRfx.png)

3. Reset your remote path like so:

`$ git remote set-url origin [new origin path goes here]`

And you should be home free to `git push` like you wanted to in the first place.


**Optional**
Just to double-check that the remote origin was changed, you can take a look with:

```
$  git remote -v
```

This should confirm the new remote origin path.

Yay! You did it!

![](https://explainxkcd.com/wiki/images/4/4d/git.png)
*comic from xkcd.com*


