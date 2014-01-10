---
layout: post
title: Multiline text and string interpolation
tags: [ruby, PogoScript, JavaScript, c#, tech]
---

While perusing some c# today (not for pleasure I might add) I noticed some pretty horrid string interpolation going on when using multiline strings.

{% highlight c#%}
  string additionalText = "another line";
  string body = @"some text
                "+ additionalText + @"
                and some more text
                ";

{% endhighlight%}

Not only is this syntax hard to read it also outputs the following:

    some text
                    another line
                    and some more text

This could of course be rewritten to:

{% highlight c# %}
  string body = string.Format(@"some text
                {0}
                and some more text
                ", additionalText);
{% endhighlight%}

Which is slightly better but precludes the use of the `{` and `}` characters.


Note how the text is all intended relative to the start of the line. However the first line is not indented at all.

Lets try this in ruby:

{% highlight ruby%}
  additionalText = "another line";
  body = <<-eos
    some text
    #{additionalText}
    and some more text
  eos
{% endhighlight%}

This will output the following:

      some text
      another line
      and some more text

Again the text is indented but at least it is consistently indented. Of course if we didn't want any indent we would have to change how we write it:

{% highlight ruby %}
  body = <<-eos
some text
#{additionalText}
and some more text
  eos
{% endhighlight %}

Which is not too bad but I think it breaks the flow of the code.

Ok just for laughs lets look at how this is usually done in JavaScript:

{% highlight javascript %}
  var body = ["some text",
             additionalText,
            "and some more text"].join("\n");
{% endhighlight %}

JavaScript doesn't support any kind of advanced string handling (as of ECMAScript 5 version 6 supports Multi-line strings and Interpolation!) so to make it readable we have to handle it using the array syntax.

*Note you can achieve multi line strings in JavaScript using the escape character to escape the line breaks
 This shouldn't be used for three reasons:*

 1. It is not supported on all run times
 2. It can break minifiers
 3. And in our case we loose the line breaks which we actually want!

So far I think ruby is doing the best job here. Multiline strings and interpolation.

The only better implementation I have seen is in [PogoScript](http://pogoscript.org)

{% highlight javascript %}
additional text = "another line"
body = "some text
        #(additional text)
        and some more text"

{% endhighlight%}

And finally we get the text indented with respect to the position of the first character of the first line of the string:


    some text
    another line
    and some more text

PogoScript compiles down to JavaScript, the actual JavaScript that this creates is:

{% highlight javascript %}
additionalText = "another line";
body = "some text\n" + additionalText + "\nand some more text";
{% endhighlight%}

