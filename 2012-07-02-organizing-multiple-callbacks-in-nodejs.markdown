---
layout: post
title: Functional Way of Abstracting Nested Callbacks
categories:
  - nodejs
  - functional-programming
  - functools
  - callbacks
  - async-programming

date: 07-02-2012
name: blog
---
Functools is a minimal JavaScript library that provides functional programming
utilities for manipulating functions and collections both synchronously and
asynchronously. I've been developing and using it in my almost all projects and
want to show you how I abstract nested callbacks with it. 

Let's assume that we'll find all HTML files in a directory and send them to a
friend via e-mail. We would have module that looks like the following one;

{% highlight javascript %}

// sendEmail.js

function findHTMLFiles(path, callback){
  // implementation
}

function readFiles(filenames, callback){
  // implementation
}

function sendEMail(files, callback){
  // implementation
}

findHTMLFiles('/home/me/docs', function(error, filenames){
  
    if(error) throw error;

    readFiles(filenames, function(error, files){
  
      if(error) throw error;

      sendEMail(files, function(error){
        
        if(error) throw error;

        console.log('OK :)');

      });

    });

});

{% endhighlight %}

Now let's call the functions defined above;

{% highlight javascript %}

findHTMLFiles('/home/me/docs', function(error, filenames){
  
    if(error) throw error;

    readFiles(filenames, function(error, files){
  
      if(error) throw error;

      sendEMail(files, function(error){
        
        if(error) throw error;

        console.log('OK :)');

      });

    });

});
{% endhighlight %}

Last year, I started coding a functional programming library for JavaScript.
There was already Underscore that provides similar FP tools but what I need was
to have a library that focuses on manipulation of both synchronous and
asynchronous functions. Besides of it, I would like to document the
dependencies of my modules when I code them and cheapest way of it is to have
very well named dependencies. When I see a NodeJS module or a web page that has
a dependency called "underscore", I have no clue of what it needs. It may need
to do some FP or iterate a collection, who knows?


There was already Underscore that provides a lot of things, and it was the
reason for me to create a new one.  In an ideal modular programming
environment, libraries should accomplish one thing and do it very well. No
matter how big it is, a library shouldn't give irrelevant things to its
clients.



Just like the majority of NodeJS coders, I always seek a better way of abstracting nested asynchronous functions during I code.

During my seeking, I created a library that gives us some tools for manipulating functions. It's called [functools](http://github.com/azer/functools) and aims to be a functional programming library that supports async functions, as well.
