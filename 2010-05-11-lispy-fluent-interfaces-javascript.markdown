---
layout: post
title: Lispy Fluent Interfaces In Javascript
categories:
  - javascript
  - lisp
  - functional
  - fluent interfaces

date: 11-05-2010
name: blog
---

By inspiring from Lisp and the functional programming utilities came with Javascript 1.6, I’ve coded a new function to iterate arrays -especially for those containing DOM nodes- by providing an alternative fluent interface and chaining. Usage examples;

{% highlight javascript %}
// log elements of an array
each( ['Hello','World'] )
  (console.log)

// disable all form elements passing additional arguments
each( document.querySelectorAll('input, select, textarea') )
  (setattr, 'disabled', true)

// apply header elements several dom manipulations
each(document.querySelectorAll('header'))
  (style, 'fontSize', '16px Arial,sans-serif')
  (style, 'background', '#ffff00')
  (style, 'padding', '3px')
  (add_class, 'Foobar')
  (add_event, 'click', function(){ alert('Hello World') })
{% endhighlight %}

And here is the source code: 
{% highlight javascript %}
/**
 * A Function Providing Lispy Iteration For Javascript
 * @author Azer Koculu <azerkoculu@gmail.com>
 */
var each = function(list)
{
  var caller = function(fninitial)
  {
    var cargs = Array.prototype.slice.call( arguments, 1);
    
    var func = function(el)
    {
      var args = [ el ];
      Array.prototype.push.apply(args,cargs);
      fninitial.apply(null, args);
    }

    Array.prototype.forEach.call( list, func );

    return caller;
  }
  return caller;
}
{% endhighlight %}
The function defined in the code above simply returns a function returning itself and taking a function with optional arguments to call it by passing the element being iterated and the optional arguments specified. Thus, the high-order-function I’ve pointed make the iteration chainable, as well. 

P.S: Functional module of the new web framework I've been working on provides some similar tools with much better implementation, which are available to be checked out; 
* [Source Code](http://github.com/azer/roka/blob/master/src/core/functional.js)
* [Tests](http://github.com/azer/roka/blob/master/test/suites/core/functional.js)
