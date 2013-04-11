---
date: 2011-12-09
title: Multiple lines in a content_for
layout: post
category: rails
comments: true
---
To make it very clear, my problem was that I wanted to include multiple javascript and CSS files but only when a certain view is accessed. For that I decided to use the [content_for][cfh] helper, which takes a block that is then yielded where asked, like this:

{% highlight erb %}
#app/views/xpto/index.html.erb
<% content_for :head do %>
  ...
<% end %>

#app/views/layouts/application.html.erb
...
<%= yield :head %>
</head>
<body>
...
{% endhighlight %}

This, will add the output of the block passed through content_for to the head of the page. However, how to add multiple lines might not be so intuitive as it seems, due to the fact that the code for the block is written in an *.erb* file. So, here it is the final version of the code, with the particularity of using %q() in order to create an escaped string.

{% highlight erb %}
<% content_for :head do %>
  <%= %q(<script src="http://blueimp.github.com/
jQuery-Image-Gallery/jquery.image-gallery.js"></script>) %>
  <%= javascript_include_tag 'jquery.fileupload', 
                             'jquery.fileupload-ui',
                             'jquery.iframe-transport', 
                             'jquery.xdr-transport' %>
  <%= stylesheet_link_tag 'jquery.fileupload-ui' %>
<% end %>

{% endhighlight %}


[cfh]: http://guides.rubyonrails.org/layouts_and_rendering.html#using-content_for