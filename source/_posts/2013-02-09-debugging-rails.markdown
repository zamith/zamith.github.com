---
layout: post
title: "Debugging Rails"
date: 2013-02-09 14:45
comments: true
categories:
---

There are many ways in which you can debug your Rails applications, I'm going to show you how to display the errors you get in a nice way, using Better Errors, and what to do when that just isn't enough.

### Better Errors

This was covered in great depth by Ryan Bates in a [RailsCasts #402](http://railscasts.com/episodes/402-better-errors-railspanel). After you install the gem, when you get an application error instead of this:

![reg-error](/images/regular_error.png)

You'll be prompted with something like this:

![better-error](/images/better_errors.png)

Note that by adding `binding_of_caller` gem you'll also have a REPL on the point of failure.

### Pry

Better errors with a REPL is going to take care of most of your problems when doing a full stack request, but if you are developing some model classes or even some libraries in Rails or plain old Ruby, you may want to declare breakpoints so you can do some debugging.

Debbuging with [pry](https://github.com/pry/pry) is as easy as droping the `binding.pry` line wherever you want the breakpoint, like so:

``` ruby
class PostsController < ApplicationController
 def index
   @posts = Post.all
   binding.pry
 end
end
```

When running this code, your server will stop at the breakpoint and you should see this:

![better-error](/images/pry.png)


This has been covered on [RailsCasts #280](http://railscasts.com/episodes/280-pry-with-rails). One thing Ryan doesn't mention though is the [pry-rails gem](https://github.com/rweng/pry-rails), which will sub in as your rails console and give you some nicities as showing routes and the schema for a given model in a good looking way.

``` ruby Pry-rails usage, from the README
$ rails console
[1] pry(main)> show-routes
     pokemon POST   /pokemon(.:format)      pokemons#create
 new_pokemon GET    /pokemon/new(.:format)  pokemons#new
edit_pokemon GET    /pokemon/edit(.:format) pokemons#edit
             GET    /pokemon(.:format)      pokemons#show
             PUT    /pokemon(.:format)      pokemons#update
             DELETE /pokemon(.:format)      pokemons#destroy
        beer POST   /beer(.:format)         beers#create
    new_beer GET    /beer/new(.:format)     beers#new
   edit_beer GET    /beer/edit(.:format)    beers#edit
             GET    /beer(.:format)         beers#show
             PUT    /beer(.:format)         beers#update
             DELETE /beer(.:format)         beers#destroy
[2] pry(main)> show-routes --grep beer
        beer POST   /beer(.:format)         beers#create
    new_beer GET    /beer/new(.:format)     beers#new
   edit_beer GET    /beer/edit(.:format)    beers#edit
             GET    /beer(.:format)         beers#show
             PUT    /beer(.:format)         beers#update
             DELETE /beer(.:format)         beers#destroy
[3] pry(main)> show-routes --grep new
 new_pokemon GET    /pokemon/new(.:format)  pokemons#new
    new_beer GET    /beer/new(.:format)     beers#new
[4] pry(main)> show-models
Beer
  id: integer
  name: string
  type: string
  rating: integer
  ibu: integer
  abv: integer
  created_at: datetime
  updated_at: datetime
  belongs_to hacker
Hacker
  id: integer
  social_ability: integer
  created_at: datetime
  updated_at: datetime
  has_many pokemons
  has_many beers
Pokemon
  id: integer
  name: string
  caught: binary
  species: string
  abilities: string
  created_at: datetime
  updated_at: datetime
  belongs_to hacker
  has_many beers through hacker

$ DISABLE_PRY_RAILS=1 rails console
irb(main):001:0>
```




