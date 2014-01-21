--- 
layout: post 
title: Testing your pogo with mocha
tags: [PogoScript, JavaScript, mocha, tech] 
---

#Testing your pogo with mocha

Writing unit tests with pogo and mocha is a lot more fun than using JavaScript but there are some interesting gotchas and some side effects that can, weirdly, actually make life easier.

## Test a synchronous method

This is a complete no-brainer.
Call your method and make your assertion:

{% highlight javascript %} 
describe 'file system'
  it 'reads a file synchronously'
    file contents = fs.read file sync('hello world.txt', 'utf-8')
    file contents.should.equal('hello world')
{% endhighlight %} 

## Test an asynchronous method
There are two ways to write this.

First of all here is the classic JavaScript style:

{% highlight javascript %} 
describe 'file system'
  it 'reads a file asynchronously' @(done)
    fs.read file('hello world.txt', 'utf-8') @(error, file contents)
      file contents.should.equal('hello world')
      done()
{% endhighlight %} 

This will work, but we are not taking advantage of any of pogos special features.

Here is a better example using the async operator `!`

{% highlight javascript %} 
describe 'file system'
  it 'reads a file asynchronously'
    file contents = fs.read file!('hello world.txt', 'utf-8')
    file contents.should.equal('hello world')
{% endhighlight %} 

Much nicer!
You will notice that we no longer call `done()` like we do in the JavaScript version. This is an interesting side effect of using the async operator.

When you call a method with the async operator pogo rewrites the function you called it from to be something similar to this:

{% highlight javascript %}
  it("reads a file asynchronously", function(continuation) {
    fs.readFile("hello world.txt", "utf-8", gen1_rethrowErrors(continuation, function(gen4_asyncResult) {
    var fileContents; 
    fileContents = gen4_asyncResult; 
    return continuation(void 0, fileContents.should.equal("hello world")); 
    }))
  });

{% endhighlight %} 

You will notice that the `continuation` parameter has been added to the test callback. When we use the async operator in a function, the containing function always gets this parameter added to it. This is to let the caller know that the function has completed.

As mocha expects a single parameter as a callback and pogo adds a callback when we use the async operator we get into the lucky situation where our `done` callback is automatically added to the test function and called when the async operation has completed.

Don't be tempted to go all "belt and braces" and add a `done` callback though. Pogo will see that you have added a parameter and add another parameter as the callback. Usually this would be want you want, just not in this case!

## Gotcha

This all works nicely in the example above. However, if the async operator is used in a subsequent callback then the test function will not have the `continuation` parameter added to it.

{% highlight javascript %} 
describe 'file system'
  it 'reads a file asynchronously'
    set timeout
      file contents = fs.read file!('hello world.txt', 'utf-8')
      file contents.should.equal('hello world')
    0
{% endhighlight %} 

In this case the `continuation` parameter is added to the function that is used to create the timeout. So in this case we need to revert to the JavaScript style and add a `done` callback and then call it once our test completes:

{% highlight javascript %} 
describe 'file system'
  it 'reads a file asynchronously' @(done)
    set timeout
      file contents = fs.read file!('hello world.txt', 'utf-8')
      file contents.should.equal('hello world')
      done()
    0
{% endhighlight %} 

## Wraping up

Pogos async operator makes working with JavaScript callbacks much easier, but it still has to play by JavaScripts rules and sometimes these cause added complications. When everything goes pear shaped understanding how async is implemented can really help.
