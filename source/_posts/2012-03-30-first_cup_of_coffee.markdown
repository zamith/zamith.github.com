---
date: 2012-03-30
title: My First Cup of Coffee
layout: post
category: coffeescript
---
Lately I've been doing and learning so much that I haven't had the time to sit down and put those ideas into "paper". Therefore, here is my first post (of hopefully many) concerning stuff I've been looking at recently.

Using javascript while doing web development nowadays is pretty much a no brainer, and on top of that you'll probably use some kind of framework such as jQuery due to browser compatibility, ease of coding or pure awesomeness. Still, even though this frameworks may add some great functionalities, most of them will not extend the language to make it more beautiful and less cluttered with punctuation you actually do not need. This is where [CoffeeScript][cs] comes in!

I'm not going to provide an extensive tutorial on coffeescript (there are far too many of those around) or list why it is great (watch Sam Stephenson's [talk][ss_talk] for that). This is just a log of a code transformation from plain old jQuery to bright and shiny CoffeeScript.

So, enough with the talk and on to the code! Here is where I started with, an actual javascript file from an actual application:

{% highlight javascript %}
$(document).ready(function($){
  $("th.ec-month-nav").live('click',function(event){
    var query_params = $(this).find("a").attr("href").split("?")[1];

    $('div#event-calendar').load("/calendar?"+query_params,function(){
      $('div.events').parent().addClass("has-events");
    });

    event.preventDefault();
  });
});
{% endhighlight %}

From my (not so great) knowledge of CS the first thing that pops out as being removable are the semicolons, braces, parenthesis and function declarations, resulting in the following:

{% highlight coffeescript %}
$ ->
  $("th.ec-month-nav").live 'click', (event) ->
    var query_params = $(this).find("a").attr("href").split("?")[1]

    $('div#event-calendar').load "/calendar?"+query_params, ->
      $('div.events').parent().addClass "has-events"

     event.preventDefault()
{% endhighlight %}

Hum... Looks pretty and it is code that actually works. But we are not quite finished yet, there are three more changes that can be made. First, we can move the `query_params` variable into the string (since CS supports ruby like string interpolation) and drop the `var` keyword because variables will be automatically scoped. Lastly, we can switch `this` to its CS counterpart `@` and we are done.

{% highlight coffeescript %}
$ ->
  $("th.ec-month-nav").live 'click', (event) ->
    query_params = $(@).find("a").attr("href").split("?")[1]

    $('div#event-calendar').load "/calendar?#{query_params}", ->
      $('div.events').parent().addClass "has-events"

    event.preventDefault()
{% endhighlight %}

This was my first time coding in CS, which means that there might be some other changes to be made in this example, but I am really happy with the way it looks. Hope I have the time to write anything else soon. 

Until then, happy coding! ;)

[cs]: http://coffeescript.org/
[ss_talk]: http://vimeo.com/35258313