---
date: 2012-07-07
title: Change the background image with animate
layout: post
category: jQuery, CSS
---

It is not possible to change the background image of a DOM element with animate (misleading title, sorry about that :P), so here is a pretty nice workaround that will get you that fade in/fade out effect you always wanted when changing images.

This works by first reducing the opacity of the current image to 0, making it invisible, and when this has finished apply the new style and making the opacity 1 again with a soft fade in effect. Nice. :D

{% highlight javascript %}
$("#image").stop().animate({opacity: 0},1000,function(){
    $(this).css({'background-image': "url('/images/alt_image.png')"})
           .animate({opacity: 1},{duration:1000});
});
{% endhighlight %}

*Note: The stop function is there just to prevent the effects queue to grow to deep, i.e, if this is triggered by a click and the user clicks multiple times, only the last animation will actually run, all the others will be stopped.*