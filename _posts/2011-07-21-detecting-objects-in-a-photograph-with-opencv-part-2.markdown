---
layout: post
title: Detecting objects in a photograph with opencv part 2
tags: [opencv, ruby, tech]
---

[![Eyes being detected using
opencv](/images/posts/detecting-objects-in-a-photograph-with-opencv-part-2.jpg "Eyes being detected using opencv")](/images/posts/detecting-objects-in-a-photograph-with-opencv-part-2.jpg)

As I [found out last
time](/detecting-objects-in-a-photograph-with-opencv/) it
is quite easy to detect objects in a photgraph. The difficult part is to
create a classifier that will do this for the particular object you are
looking for.

I am still working on building a simple object classifier and I will
share how I have done this soon. In the mean time you may like to whet
your appettite with some of the classifiers that ship as part of opencv.

You can find classifiers that can detect faces, eyes and whole bodies in
[opencvs source
control](https://code.ros.org/svn/opencv/branches/2.3/opencv/data/haarcascades/).
For the purpose of demonstration I will use the [eye
detector](https://code.ros.org/svn/opencv/branches/2.3/opencv/data/haarcascades/haarcascade_eye.xml).
I am also using ruby-opencv from [this
repository](https://github.com/ser1zw/ruby-opencv).

In your ruby file (eyes.rb for example) include opencv:

{% highlight ruby %}
require "opencv"
{% endhighlight %}

Load the classifier into opencv:

{% highlight ruby %}
classifier = 'haarcascade_eye.xml'
detector = OpenCV::CvHaarClassifierCascade::load(classifier)
{% endhighlight %}

Load the image you want to run the classifier against:

{% highlight ruby %}
image = OpenCV::IplImage.load('sample_image.jpg')
{% endhighlight %}

We can now detect objects by:

{% highlight ruby %}
detector.detect_objects(image) do |region|
  color = OpenCV::CvColor::Blue
  image.rectangle! region.top_left, region.bottom_right, :color => color
end
{% endhighlight %}

This will draw blue rectangles over around the eyes. We can then save
that image:

{% highlight ruby %}
image.save_image('eyes_detected.jpg')
{% endhighlight %}

The result? Yours truly with square blue glasses!