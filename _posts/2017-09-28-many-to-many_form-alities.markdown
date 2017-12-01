---
layout: post
title:      "Many-to-many form-alities"
date:       2017-09-28 17:34:27 -0400
permalink:  many-to-many_form-alities
---


With my brain finally wrapped around [building objects and tables correctly for a many-to-many relationship](http://marielfrank.com/2017/09/28/the_many-to-many_eat-off/), I came to a much more complicated challenge in [my Sinatra project](https://github.com/marielfrank/dineable-app)--an ERB form and Sinatra `POST` route that could do the following:

1. Create a new `Restaurant` instance
2. Associate the `Restaurant` instance with its `User` instance
3. Accept properties such as "name" or "address" that would be fed as parameters to the `RestaurantController`, assigned, and could later be retrieved by `Restaurant` instances: `@mamouns.address = "St. Mark's Place, NYC"`
4. Associate the `Restaurant` instance with one or more existant `DietPref` instances through checkboxes
5. Allow the user to add a new dietary preference and then associate it with that `Restaurant` instance

Here's how my final, working code looked:

**The restaurants/new.erb form***


```
<form class="form" action="/restaurants" method="post">
    <label for="name">Name</label><br>
    <input type="text" name="restaurant[name]">
    <br><br>
    <label for="address">Address</label><br>
    <input type="text" name="restaurant[address]">
    <br><br>
    <h3>Dietary Preferences Accommodated</h3>
    <label for="diet_prefs">Select an existing preference</label><br>
    <% DietPref.all.each do |pref| %>
      <input type="checkbox" name="restaurant[diet_pref_ids][]" value="<%= pref.id %>" id="<%= pref.id %>">
        <%= pref.name %>
      </input>
      <br>
    <% end %>
    <br>
    <label for="diet_pref_name">Or add a new one</label><br>
    <input type="text" name="diet_pref[name]" id="diet_pref_name">
    <br><br>
    <input class="btn btn-primary" type="submit" value="Add Restaurant">
</form>
```


**The RestaurantController route**
```
  # create restaurant instance based on form values
  post '/restaurants' do
    # ensure restaurant's name field was filled out
    if !params[:restaurant][:name].empty?
      # create restaurant from restaurant key in params hash
      @restaurant = Restaurant.create(params[:restaurant])
      # set restaurant's user as current user
      @restaurant.user = current_user
      if !params[:diet_pref][:name].empty?
        # if new dietary preference was added, create new DietPref
        # and associate it with the restaurant, then save
        @restaurant.diet_prefs << DietPref.new(name: params[:diet_pref][:name])
        @restaurant.save
      end
      # send user to restaurant's show page
      flash[:message] = "Nice! You added a new restaurant :)"
      redirect "/restaurants/#{@restaurant.slug}"
    else
      # remind user to add name to restaurant if name given
      flash[:message] = "Seems like you forgot to add the restaurant's name!"
      redirect '/restaurants/new'
    end
end
```

Truly, the route was the easy part. The real challenge was the form, particularly this part:

```
<% DietPref.all.each do |pref| %>
   <input type="checkbox" name="restaurant[diet_pref_ids][]" value="<%= pref.id %>">
      <%= pref.name %>
   </input>
   <br>
<% end %>
```


I wasn't sure if this was going to work the same way for a many-to-many relationship as it does for a one-to-many relationship (i.e., if Restaurant `has_many :diet_prefs` while DietPref `belongs_to :restaurant`), but as luck would have it, it does.

The way it works:

`name` in `input` tells the `params` hash where to place the `value` or `id` of the preference's id. What we're doing is saying, "hey params hash, add this dietary preference id into an array of dietary preference ids that restaurant is keeping track of." (The key here is to remember that our restaurant gets those ids through the RestaurantDietPref object, so we don't need any additional go-betweens.) Granted, that extra empty array (`[]`) at the end of the `name` is a bit confusing. It seems like ERB just knows that it's not supposed to be an empty array after the first id has been added.

So let's say I have a restaurant named "Mamouns" with an address of "St. Mark's Place, NYC" that is both vegetarian with an id of "1" and gluten free with an id of "2" (both of which had been previously added as dietary preferences through other restaurants), then our params hash would look like:

```
{ :restaurant => { :name => "Mamouns", :address => "St. Mark's Place, NYC", :diet_pref_ids => ["1", "2"] } }
```

thus allowing us to run this statement to create the Restaurant instance:

```
@restaurant = Restaurant.create(params[:restaurant])
```

It turned out adding a new dietary preference was much simpler because the data doesn't sit inside `:restaurant` in the `params` hash. The only issue was that I wasn't able to find a cleaner solution than `@restaurant.diet_prefs << DietPref.new(name: params[:diet_pref][:name])` to create the association.

And that's pretty much it!
