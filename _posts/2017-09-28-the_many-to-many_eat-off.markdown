---
layout: post
title:  "The Many-to-Many Eat-Off!"
date:   2017-09-28 14:32:54 -0400
---


> Join tables: [they're not your friend...or they could be](https://www.youtube.com/watch?v=keP4MBI1taY).

I'll be the first to admit that join tables were ***not*** my favorite part of learning SQL or Active Record. I could definitely understand their utility (you need to keep track of those ids somewhere), but I hated creating Ruby classes that seemed nonsensical. I mean really, what the heck is a `DancerTheater` instance that `belongs_to` both a `Dancer` instance and a `Theater` instance? 

## A join table by any other name

And then there were the naming conventions. It's easy enough to remember that a Ruby class named `Cat` will be linked with a SQL table `cats` requiring a migration called `create_cats` that magically becomes `CreateCats` as the migration class. And in turn you easily `build` an instance of `Cat` through an `Owner` that `has_many` of this `Cat` object with `Owner.cats.build(name: "Ghoti", breed: "orange tabby")`.

Simple enough, right?

But it certainly starts getting confusing when you have a Ruby class, as I did for my Sinatra project, called `RestaurantDietPref` with a SQL table `restaurant_diet_prefs`, requiring a migration entitled `create_restaurant_diet_prefs` all in order to link (you guessed it!) my `DietPref` (i.e., dietary preference) and `Restaurant` classes. 

## The many-to-many eat-off begins
![](http://reactiongifs.me/wp-content/uploads/2013/12/hungry-eating-chicken-wings-competition-mr-bean-rowan-atkinson.gif)

Thankfully, `has_many :through` is a thing, meaning that nobody has to write some garbage like

`@restaurant.restaurant_diet_prefs.diet_prefs` 

and then another statement selecting the specific `diet_prefs` where the `restaurant_id = @restaurant.id`. Phew! But I was still confused about how to actually set up my models until I found Josh Susser's [Many-to-many Dance-off!](http://blog.hasmanythrough.com/2006/4/20/many-to-many-dance-off). This piece is from 2006, but is somehow the only piece I could find to provide real examples of how to create both the migration for the join table and the models for a many-to-many relationship. Here's what my `has_many :through` setup looks like:

**Join Table Migration: 20170925213446_create_restaurant_diet_prefs.rb**

```
class CreateRestaurantDietPrefs < ActiveRecord::Migration[5.1]
  def change
    create_table :restaurant_diet_prefs do |t|
      t.integer :restaurant_id
      t.integer :diet_pref_id
    end
  end
end
```

**Restaurant Model**
```
class Restaurant < ActiveRecord::Base
  belongs_to :user
  has_many :restaurant_diet_prefs
  has_many :diet_prefs, :through => :restaurant_diet_prefs
end
```

**DietPref Model**
```
class DietPref < ActiveRecord::Base
  has_many :restaurant_diet_prefs
  has_many :restaurants, :through => :restaurant_diet_prefs
  has_many :users, :through => :restaurants
end
```

**RestaurantDietPref (Join Table) Model**
```
class RestaurantDietPref < ActiveRecord::Base
  belongs_to :restaurant
  belongs_to :diet_pref
end
```

And that's how you get a many-to-many join table set up with foreign keys and get your models working as they should. Perhaps you can imagine the relief when my first `@user` was able to retrieve all dietary preferences that they were associated with through a simple `@user.diet_prefs`.

I'll cover the Sinatra/ERB form complexities resulting from many-to-many relationships in my next post ;)
