---
layout: post
title: User authorisation with Sinatra
tags: [authorisation, ruby, tech]
---

Today I needed to add authorisation of specific routes to my sinatra
application. After having a look around I couldn’t find anything I
really liked. So I sat down and did the same thing any self respecting
developer would – and wrote one myself.

I was actually surprised how easy it was and the result is
[simple-authorisation](https://rubygems.org/gems/simple-authorisation)
(`gem install simple-authorisation`).

To include this in your app simply:

{% highlight ruby %}
  require 'simple-authorisation'
{% endhighlight %}

And then in your sinatra config:

{% highlight ruby %}
  configure do
    Simple::Authorisation.route '/', :deny => ['?'], :allow => ['*']
    Simple::Authorisation.route '/login', :allow => ['?']
    Simple::Authorisation.route '/logout', :allow => ['?']
    Simple::Authorisation.route '/admin', :allow => ['*'], :deny => ['?']
  end
{% endhighlight %}


Adding configuration for a route / will actually apply it to all its
child routes. Adding additional routes will overwrite that setting. ‘?’
means anonymous users and ‘\*’ means any logged in user.

The last thing you need to do is add a current\_user method to your
application:

{% highlight ruby %}
  class Application < Sinatra::Application
    def current_user
      session[:user]
    end
  end
{% endhighlight %}

Now simple-authorisation knows how to get the current user it can do its
thing!

I know this is very basic but so far it is all I need. The next thing to
add will be specific user groups.

If you like the look of this and have something to add then please [fork
this on github](https://github.com/dereke/simple-authorisation).
