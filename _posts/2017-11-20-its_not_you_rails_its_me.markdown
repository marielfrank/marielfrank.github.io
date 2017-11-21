---
layout: post
title:      "It's not you, Rails; it's me"
date:       2017-11-21 01:12:28 +0000
permalink:  its_not_you_rails_its_me
---


I had been told over and over that Rails is magical. But I would argue that Rails is just macro-ful. 

Don't get me wrong--I'm not a Rails hater. I just don't LOVE it or #feelthemagic like a lot of other Rubyists seem to. You know when all of your friends hype up a TV show or movie so much that when you finally see it you aren't especially impressed? And you're left with a somewhat empty feeling of, "...this is it?" That's been Rails for me. 

I was fully enamored by [Sinatra](https://www.sitepoint.com/uno-use-sinatra-implement-rest-api/). I lived and breathed that DSL. 

![](http://media.riffsy.com/images/2cee6f9e4c7f85402f532d2057a3c580/tenor.gif)

**In comparison, Rails just feels meh to me.**


![](https://i.imgur.com/V9gXd9z.gif)


Yes, there is power in Rails. It's great for refactoring--I'll give it that. 

I appreciate that I can do:

**`User#new`**

`<%= render partial: 'form', locals: {user: @user, submit_text: "Sign Up"} %>`

**`User#edit`**

`<%= render partial: 'form', locals: {user: @user, submit_text: "Edit Profile"} %>`


**`users/form`**
```
<%= form_for user do |f| %>
    <%= f.label :name %><br>
    <%= f.text_field :name %><br>
    <%= f.label :email %><br>
    <%= f.email_field :email %><br>
    <%= f.label :password %><br>
    <%= f.password_field :password %><br>
    <%= f.label 'Are you an admin?' %>
    <%= f.check_box :admin %><br>
    <%= f.submit "#{submit_text}", class: 'btn cta' %>
<% end %>
```


But it's, well, particular. That magic (i.e., the mass of macros) breaks down as soon as you try to add in more flexibility. 

My attempts to get an object to validate and add errors from its nested child have been anything but breezy. Tell me, if there are already validations for the child's attributes in the child class, why on earth would I need to write them again within the parent's custom nested attribute setter method?

Any why do I have to write all of my routes in a completely different part of my application than where the controllers hang out? It's double the work. Blech.

Granted, I'm a beginner, I have a lot to learn. And I'm completely open to being blown away by some hidden Rails features that I have yet to learn about. For now though, I'm looking forward to digging into a new framework.
