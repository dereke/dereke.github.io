---
layout: post
title: Compiling PogoScript
tags: [PogoScript, JavaScript, browserify, karma]
---

[PogoScript](http://pogoscript.org) is the best way to write JavaScript, but there is not much information about how to compile it so I've written down a few of the things that I have tried and the results that I got.

# Let browserify do the heavy lifting by using the pogoify plugin

[Browserify](http://browserify.org) is a fantastic way of managing dependencies in JavaScript apps. Using the [pogoify](https://github.com/featurist/pogoify) plugin we can have browserify compile into one nice output file. You you can also use browserify in karma to compile your test scripts which lets you have Pogo everywhere (nice!). 

You can add this configuration to your gruntfile to compile pogo using pogoify


{% highlight javascript %}
browserify: {
  dist: {
    files: {
      'build/app.js': ['lib/app.pogo'],
    },
    options: {
      transform: ['pogoify'],
      extensions: ['.pogo']
    }
  }
}
{% endhighlight%}

We use the [karma-browserify](https://github.com/xdissent/karma-browserify) plugin in our karma.conf.js:

{% highlight javascript %}
frameworks: ['mocha', 'chai', 'browserify'],

files: [
  'test/**/*Spec.pogo'
],

browserify: {
  transform: ['pogoify'],
  extension: ['.pogo'],
  watch: true
},

preprocessors: {
  'test/**/*.pogo' : 'browserify'
},
{% endhighlight%}

While this setup works really well on a small project there is a big performance downside once your project gets bigger. I found that when trying to run the tests in karma it would just hang while all the pogo was compiled.

# Compile intermediary files using grunt-pogo
To get better performance compile the pogo to an intermediary JS files. Doing this isolates the slowest part of the build process - compiling pogo. It can also be handy to have a reference as to what the pogo is compiling down to. In this solution we continue to use karma-browserify but only to require dependencies. 

You can use the [grunt-pogo](https://github.com/leecrossley/grunt-pogo) plugin to do the compilation:

{% highlight javascript %}
pogo : {
  compile: {
    files: [{
      expand : true,
      cwd: '.',
      src: [
        'lib/**/*.pogo',
        'test/**/*.pogo'
      ],
      dest: 'build/',
      ext: '.js'
    }],
  }
},

watch: {
  all: {
    files: ['lib/**/*.pogo', 'test/**/*.pogo'],
    tasks: 'pogo'
  }
}

{% endhighlight%}

The karma.conf.js looks slightly different as we no longer need to use pogoify:

{% highlight javascript %}
frameworks: ['mocha', 'chai', 'browserify'],
files: [
  'build/test/**/*Spec.js'
],
browserify: { watch: true },
preprocessors: { 'build/test/**/*.js' : 'browserify' },
{% endhighlight%}

# Even better performance with gulp
You can get an even better result by using [gulp](http://gulpjs.com) with the [gulp-pogo](https://github.com/dereke/gulp-pogo) plugin. We will again write intermediary files but while grunt will compile all the pogo when a file changes gulp will only compile the changed files.

We add a pipeline for lib and test to the gulp task:

{% highlight javascript %}
gulp.task('compile', function(){
  watch({glob: ['./test/**/*.pogo'], emitOnGlob: true})
    .pipe(plumber())
    .pipe(pogo())
    .pipe(gulp.dest('./build/test/'));

  watch({glob: ['./lib/**/*.pogo'], emitOnGlob: true})
    .pipe(plumber())
    .pipe(pogo())
    .pipe(gulp.dest('./build/lib/'));
})
{% endhighlight%}

The karma.conf.js can stay the same as when using grunt-pogo solution.

# Conclusion
Having tried all of these options I have found the gulp option to be the best setup. Changes to either the tests or application are compiled quickly which allows karma to pick up the changes and run the tests with the smallest possible delay.
