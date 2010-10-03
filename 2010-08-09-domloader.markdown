---
layout: post
title: DOMLoader, A Javascript Library Making Easier To Handle Frontend Dependencies of Web Apps
categories:
  - domloader
  - projects
  - javascript
  - css
  - dependency injection


date: 08-08-2010
---
_**Update:** DOMLoader has recently been [restructured](http://github.com/azer/domloader/compare/25fb6f1d56...f220cb663a) and
now it lies upon [CommonJS modules](http://commonjs.org/specs/modules/1.0) being built by the new tool I've developed,
[JSBuild](http://github.com/azer/jsbuild). After the post below, I started to improve it to announce a better version with
some cool features like custom dependencies, aliases etc. and now it's finally ready to announce. Since it tells about the
idea behind DOMLoader project and demonstrates its basic usage, this blog post is still not out of date._ 

I'm glad to announce the new library I've been working on; DOMLoader, which does handle dependencies of web applications by
providing a simple dependency injection mechanism using dependency definitions from XML or JSON files. The objective of this
project is to increase reusability and maintainability of the building blocks of web projects without increasing
complexity. In detail, here are some aims and goals of DOMLoader; 

* *Decoupling:* This approach also lets you to design elements like widget,plugin, application etc. of your project completely seperated and make lifecycle
  of elements like widget, plugin, application etc. of a project to be handled using references.

* *Low Coupling*: DOMLoader is an unobtrusive library and does depends on nothing except JSON or XML support of the web
  browser running it. What is more is that, the DOMLoader itself can be replaced with an alternative mechanism easily.

* *Reusability:* The dependency definition files that DOMLoader bases can be accessed and processed by most of the major
  browsers, backend apps,  build automation tools, command line interfaces, anygq platform where you can read any data
  serialization format...

## Getting Started

To get started to use DOMLoader, the first thing to do is to [download latest version of
DOMLoader](http://github.com/azer/domloader/downloads) and put it in a directory which
can be accessible from the web page from which we will import it. To take advantage of DOMLoader, I create a directory tree
like as follows for every project keep isolated constituents of my projects:
 
{% highlight yaml %}
- /my_project:
    - apps/
    - lib/
    - widgets/
{% endhighlight %}

Now let’s get our hands dirty. Assuming that we’ll code one or more web applications which will be consisting of several DOM
widgets demanded to be pluggable and isolated from other widgets and the application including it, we’re starting coding our
new project from developing the widgets firstly.  Here it comes the first one:

{% highlight xml %}
/my_project/widgets/foobar/index.xml

<widget>
  <dependencies>
    <script src=”foobar.js” />
    <stylesheet src=”foobar.css” />
  </dependencies>
</widget>
{% endhighlight %}

## Import

In the example above, I’ve started defining dependencies by coding a wrapper element named “widget”. Note that, it’s possible
to use “index” and “application” aliases for wrapper elements as well. Then, we put the elements storing source uri’s of the
dependencies in another element named “dependencies”. As you guess, script (you can also use “module” alias instead) and
stylesheet represent javascript and CSS documents respectively. Now let’s code a web page importing this widget:
   
{% highlight html %}
foobar.html

<!DOCTYPE html>
<html>
  <head>
    <script type=”text/javascript” src=”domloader.js”></script>
    <script type=”text/javascript”>
      domloader.load('path/to/my_project/widgets/foobar/index.xml',function(){
        var fb = new FoobarWidget();
        /* … */
      });
    </script>
  </head>
  <body>
  </body>
</html>
{% endhighlight %}

## Nesting

It’s that simple to import a widget with all of the dependencies. But as you expect, we need to make things a little
complicated to designate advantage of using DOMLoader:

{% highlight xml %}
/my_project/widgets/foobar-wrapper/index.xml

<widget>
  <dependencies>
    <script src=”foobar-wrapper.js” />
    <stylesheet src=”themes/default/main.css” />
    <widget src=”../foobar/index.xml” />
  </dependencies>
</widget>
{% endhighlight %}

This example above demonstrates creating a widget wrapping another one, using widget element to import dependencies of
another widget. As you guess, widget is just alias indicating index dependency in the example above, which means, 5. line is
equivelent of this two import examples: 

{% highlight xml %}
<index src=”../foobar/index.xml” />

<application src=”../foobar/index.xml” />
{% endhighlight %}

## Object Dependencies

To demonstrate another type of dependency, object dependencies, we're going to create one more widget named baz:

{% highlight xml %}
/my_project/widgets/baz/index.xml

<widget>
  <dependencies>
    <script src=”baz.js” />
    <object name=”jQuery” src=”http://ajax.googleapis.com/ajax/libs/jquery/1.4/jquery.min.js” />
  </dependencies>
</widget>
{% endhighlight %}

As you’ve noticed, I’ve defined jQuery as an object dependency in the fourth line of the example above. Since it’s possible
to duplicate dependency definition of some commonly used javascript libraries in several index documents being imported in
same web page, we can test whether a library putting its context in a global variable is loaded before or not.  As you
expect, DOMLoader will load jQuery’s source if only global context has not a variable named jQuery.  Besides of basic global
variable testing, property elements make possible to add more specific conditions using regular expressions:

{% highlight xml %}
<object
  name='jQuery'
  src=”http://ajax.googleapis.com/ajax/libs/jquery/1.4/jquery.min.js”>
  <property name='jQuery.fn.jquery’ match=’1.4.[2-9]' />
</object>
{% endhighlight %}

The only thing remaining to be done to complete our example is definition of an application gathering some widgets now, which
doesn’t differ from widget or other index definitions. I guess we’re now ready to get it done;

{% highlight xml %}
/my_project/apps/hello_world/index.xml

<application>
  <dependencies>
    <widget src='../../widgets/foobar-wrapper/index.xml' />
    <widget src='../../widgets/baz/index.xml' />
  </dependencies>
</application>
{% endhighlight %}

And here is the example of importing the application we’ve defined above, almost same as the widget import example:

{% highlight html %}
hello_world.html

<!DOCTYPE html>
<html>
  <head>
    <script type=”text/javascript” src=”domloader.js”></script>
    <script type=”text/javascript”>
      domloader.load('path/to/my_project/apps/hello_world/index.xml',function(){
        var fwb = new FoobarWrapperWidget();
        var baz = new BazWidget();
        /* … */
      });
    </script>
  </head>
  <body>
  </body>
  </html>
{% endhighlight %}

Since I’ve coded these examples using metasyntactic namings to make it easier to read and understand, it’s not available to
download but you can check [these working examples](http://github.com/azer/roka-examples) and also several example documents coded for testing can be found at the
project repository.

I hope this document would be helpful to get started using DOMLoader in your projects. In the next blog posts I’ll tell about
using JSON and other formats like YAML and another feature of DOMLoader providing namespace initialization which will be very
important to take advantage of the [“async” attribute coming with
HTML5](http://www.whatwg.org/specs/web-apps/current-work/#attr-script-async). 

Nevertheless, please feel free to share your opinions and any kind of contributions with me. For now, here are the another
pages/examples would be helpful to learn more about DOMLoader:

* [Source Code](http://github.com/azer/domloader/tree/master/src)
* [Tests](http://github.com/azer/domloader/tree/master/test)
* [Usage Examples](http://github.com/azer/roka-examples)

vim: tw=125
