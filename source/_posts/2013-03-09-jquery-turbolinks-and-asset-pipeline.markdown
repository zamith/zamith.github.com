---
layout: post
title: "jQuery, Turbolinks and Asset Pipeline"
date: 2013-03-09 21:19
comments: true
categories: [rails, javascript]
---

I've talked about turbolinks [before](http://blog.zamith.pt/blog/2012/09/28/turbolinks/), and it is awesome. Integrating it with jquery plugins or legacy code could be quite a [pain](http://reed.github.com/turbolinks-compatibility/), so jquery.turbolinks came to be.

It's a pretty simple plugin that stores each callback you had on `DOM ready` and runs it on `page:change`, the turbolinks event.

So, you might think all your problems are solved, but it is not quite so. What if you want to run some code just for a specific page? Or include a file for a specific page?

Using turbolinks render the usage of stuff like `content_for :head` impossible, as the head will already be on cache, and the code you added to it will not run.

And if you try to add the code in the body, jquery.turbolinks will add the callback each time you go to the page, which means that if you visit the same page twice before a full page refresh, your code will also run twice. Not very good...

### Rails 3.1 asset pipeline

The asset pipeline has three goals, precompile, concatenate and minify.

So, even though the rails generators create a .coffee file for each controller, the normal usage would be to have all of them concatenated into one, when in production. This file will be loaded on the home page and can be cached the rest of the way.

There are obviously some times when we might want to add some files in other places than the home page. For that we'll have to create a new manifest files and explicitly tell rails to add it to the precompile path. A common usage for this is to have a `footer.js` manifest for files that are loaded after the rest of the body.

If we want to add such manifests to the body, using turbolinks and jquery.turbolinks, we'll have the same problems if the code is actually trying to run code, and not just defining functions and variables.

### The pipeline trap

A problem with having all you're files concatenated is that you have to be extra careful with when and where you attach event listeners. For instance, if you attach an event to an li in the `posts.coffee` file, and it is then included in the `application.js` manifest, all the `li`'s in your application will have that listener.

There are two ways (that I know of) of solving this problem.

You can either add a class or id to the `body` element of each page, such as `posts_index` and then attach the listeners to the `li`'s only when the `body` has that attribute.

``` coffeescript app/assets/javascripts/controller_name.coffee
$ ->
  if $('body.controller_name_action_name').length > 0
    // Do some stuff here
```

``` erb app/views/layouts/application.html.erb
<!DOCTYPE html>
<html>
<head>
  <title>AppName</title>
  <%= stylesheet_link_tag    "application" %>
  <%= javascript_include_tag "application" %>
  <%= csrf_meta_tags %>
</head>
<body class="<%= "#{params[:controller]}_#{params[:action]}" %>">

<%= yield %>

</body>
</html>
```
Or you can encapsulate all your code into functions, and then make them visible in the page you want to call them on.

``` coffeescript app/assets/javascripts/controller_name.coffee
$ ->
  random_function = ->
    console.log "Hello World"

  window.exports ||= {}
  window.exports.another_random_name = random_function
```

You need to export the functions because coffeescript will not let functions and variables defined in a file pollute the global scope, and the easiest way to do it a browser is to attach them to the `window` object which is always present. We add an `exports` namespace so that the `window` namespace will not be polluted with all our functions, but it would work even without it.

I tend to prefer the second approach for two reasons. First, and even though it adds JavaScript to the body, it is less obtrusive. And second, because it integrates with turbolinks, as it does not assume the code in the head is run every time.

### The Turbolinks Problem

We still have a problem, though. Code that's in the body running more than once.

The easier way I found of solve this was to use the once function from the `underscore` library. Obviously I was already using `underscore` for other stuff, or else it would be a complete overkill. If you just need this one function, define it somewhere that gets concatenated into your `application.js` file.

So, calling the function I defined above would look like this:

``` html app/views/controller_name/action_name.html.erb
<script>
  jQuery(_.once(function($){
    window.exports.another_random_name();
  }));
</script>
```

And if you want to use the once function, give credit to Jeremy Ashkenas, Brad Dunbar and [many others](https://github.com/documentcloud/underscore/graphs/contributors), and go ahead and copy paste.

``` javascript _.once function
_.once = function(func) {
  var ran = false, memo;
  return function() {
    if (ran) return memo;
    ran = true;
    memo = func.apply(this, arguments);
    func = null;
    return memo;
  };
};
```



