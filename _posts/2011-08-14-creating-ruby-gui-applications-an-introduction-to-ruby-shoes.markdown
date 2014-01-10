---
layout: post
title: Creating ruby gui applications – an introduction to ruby shoes
tags: [gui, ruby, tech]
---

As part of my effort to [detect objects using
opencv](/detecting-objects-in-a-photograph-with-opencv)I
have found myself performing a mind numbing task and as every lazy
programmer knows this is the time to build a tool.

After a bit of investigation into the various gui libraries for ruby I
have found one that seems pretty good called Ruby Shoes. There seem to
be a few variants of this particular library. Strangely the classic [Red
Ruby Shoes](https://github.com/shoes/shoes) version does not act like a
regular ruby library. It seems to be a runtime that uses ruby.
The [Green Shoes](https://github.com/ashbb/green_shoes) variant however
is a straightforward ruby library. You can *gem install
green\_shoes* and get up and running straight away.

A gui application in Ruby Shoes is called a shoe app and is started by
calling:


{% highlight ruby %}
  Shoes.app do
    # application code goes here
    title "Hello World!"
  end
{% endhighlight %}

As I want to create an image selection tool I need to be able to track
mouse clicks, movements and releases and then draw a rectangle as this
is going on.

After playing around for a while I came up with this:

{% highlight ruby %}
  require 'green_shoes'
  Shoes.app  do
    title "Selection tool"
    image("your_image.jpg").click do |button, click_left, click_top|
      mouse_down = true
      release do
        mouse_down = false
      end

      nofill
      @selection = rect :left => mouse[1], :top => mouse[2], :width => 1, :height => 1

      motion do |move_left, move_top|
        if mouse_down
          width =  move_left - click_left
          height =  move_top - click_top

          width = 1 if width < 1
          height = 1 if height < 1

          @selection.style(:width => width, :height => height)
        end
      end
    end
  end
{% endhighlight %}


There are probably more efficient ways to handle the release but this
was the best I could come up with in half an hour.

I found [the manual](http://shoes.heroku.com/manual/Hello.html) quite
helpful to figure all this out.
