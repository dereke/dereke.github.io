---
layout: post
title: Detecting objects in a photograph with opencv
tags: [opencv, ruby, tech]
---

[![Two faces being detected using
OpenCV](/images/posts/detecting-objects-in-a-photograph-with-opencv.jpg "OpenCV Face Detection")](/images/posts/detecting-objects-in-a-photograph-with-opencv.jpg)

For a project I am working I need to detect simple rectangular objects
in a photograph. After some investigation I have settled on using the
Open Computer Vision (opencv) libraries to do this. I have played around
with opencv a little in the past but still consider myself a newbie.
Having said that it has not been too hard to get up and running.

My programming language of choice is ruby so I am making use of the
ruby-opencv gem. Unfortunately this gem doesn’t seem to be very well
supported and didn’t install using good old *gem install opencv.* What I
found was that [this
repository](https://github.com/ser1zw/ruby-opencv) seemed to be the most
up to date fork of code. So I cloned it and went about getting it
installed.

ruby-opencv is a native gem that wraps the opencv libraries so there is
not much ruby going on in there and plenty of c++. After muddling my way
through a few different messages about dependencies I did not have and
some confusing error messages – which again where simply unmet
dependencies I finally managed to get the gem built.

The repository has a sample that shows how to do face detection. It is
one of those pieces of code that make you go *what the heck! it can’t be
that simple!* And while you can do face detection in about 8 lines of
code it is not really the code that is the magic part here.

The face detection algorithm is actually a set of Haar-like features.
This is basically a description of what sort of object we are looking
for – but go read [the wikipedia
article](http://en.wikipedia.org/wiki/Haar-like_features) for a more
comprehensive description. In order to detect a specific type of object
we are going to have to write our own classifier that describes what we
are looking for.

The next step is to write a classifier for our objects which we will do
in the next post.

 

