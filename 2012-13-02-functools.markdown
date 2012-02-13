---
layout: post
title: Functional Way of Avoiding Nested Callbacks in JavaScript
categories:
  - javascript
  - nodejs
  - functional-programming
  - functools
  - callbacks
  - async-programming
  - function-composition
  - juxtaposition

date: 13-02-2012
name: blog
---
[Functools](http://github.com/azer/functools) is a minimal JavaScript library
that provides functional programming utilities for manipulating functions and
collections both synchronously and asynchronously. I've been developing and
using it in my almost all projects and want to show you how I avoid nested
callbacks using it. 

[Function
composition](http://en.wikipedia.org/wiki/Function_composition_%28computer_science%29)
is the first technique (and my favorite one) that I'll explain. To give an
example for it, assume that we want to find all HTML files in a directory, read
their content and send them to a friend via e-mail.

{% highlight javascript %}

// sendEmail.js

function findHTMLFiles(path, callback){
  implementation++;
}

function readFiles(filenames, callback){
  implementation++;
}

function sendToAFriend(files, callback){
  implementation++;
}

{% endhighlight %}

You may notice that the last two functions above take what its previous
function produce, with no need of any modification. Which makes them pretty
suitable for function composition:

{% highlight javascript %}
var compose = require('functools').compose;

compose.async(findHTMLFiles, readFiles, sendToAFriend)('/home/me/docs', function(error){
  if(error) throw error;

  console.log('OK :)');
});
{% endhighlight %}

Looks much simpler compared to a regular code with 3 nested callbacks. Function
composition may also remind you method chanining.

Second technique I would like to mention is
[juxtaposition](http://clojuredocs.org/clojure_core/clojure.core/juxt).
Functools has both sync and async implementations of juxt. Even if you haven't
used it yet on your projects, I think following example will be enough to give
the whole idea of it:

{% highlight javascript %}
function foo(callback){
  setTimeout(callback, 100, 'foo');
}

function bar(callback){
  setTimeout(callback, 250, 'bar');
}

function qux(callback){
  setTimeout(callback, 50, 'qux');
}
{% endhighlight %}

So, we have the assume async functions above and need to take all the content of them in a minimalistic way;

{% highlight javascript %}

var juxt = require('functools').juxt;

juxt.async({ 'foo':foo, 'bar': bar, 'qux': qux)(function(error, results){
  if(error) throw error;

  assert.equal(results.foo, 'foo');
  assert.equal(results.bar, 'bar');
  assert.equal(results.qux, 'qux');
});

{% endhighlight %}

Other powerful tools I like using are map, filter and reduce functions. As you
expect, both sync and async implementations of them exist in Functools. 

To give an example for map and reduce, assume that we have a list of filenames
and want to merge the content of them. Here is the implementation using
Functools:

{% highlight javascript %}

var functools = require('functools'),
    map = functools.map,
    reduce = functools.reduce;

var filenames = ['/home/me/docs/foo', '/home/me/docs/bar', '/home/me/docs/qux'];

function readFile(path, callback){
  implementation++;
  callback(undefined, content);
}

function merge(a, b){
  return a + '\n' + b;
}

map.async(readFile, filenames, function(error, contents){

  var all = reduce(merge, contents);
  
  console.log(all); // puts foo\nbar\nqux

});

{% endhighlight %}

To summarize, Functools has async implementations of some powerful functional
programming tools that can let us avoid nesting callbacks. 

There are more examples at the [homepage of
Functools](http://github.com/azer/functools). Besides of the documentation of
it, you may also take a look at
[Combiner](https://github.com/azer/combiner/blob/master/lib/combiner.js), a
command-line tool and library for finding and manipulating files. It's based on
a middleware that lets us initialize different layers of map and filter
functions.

It would also be very helpful to check the [source code of Functools
itself](https://github.com/azer/functools/blob/master/lib/functools.js).
You'll notice that it uses map and reduce functions a lot, to implement its
remaining functionalities.

Please feel free to share your thoughts, recommandations and examples.
