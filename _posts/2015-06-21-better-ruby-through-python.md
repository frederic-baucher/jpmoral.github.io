---
layout: post
title: "Better X by learning Y"
date: 2015-06-21
categories: ruby python
---
#### (or, how I learned what enumerators are for)

The Enumerable[^enumerable] module is one of the most used in Ruby.  I often read its docs to see what methods are available and how they're used.  One phrase that jumped out at me when I first started Ruby was

> If no block is given, an enumerator is returned instead.

This shows up in various forms in a lot of the method descriptions.  According to the docs Enumerator[^enumerator] is

> A class which allows both internal and external iteration.

I was puzzled.  Calling, say, `each_with_index` without a block argument returned an enumerator which I could then later iterate over.  I thought, why not just pass the block in the first place and be done with it?  I filed the question away in my head.

Some two years later I took Peter Norvig's Design of Computer Programs class on Udacity[^udacity].  There I encountered Python's awesome generators and iterators.  I'm not going to go into detail here, I'll just say that these tools allow lazy evaluation of collection[^foot] items, which means you don't have to load the entire collection all at once.

I found this very exciting, but for some reason I didn't make the connection to Ruby's Enumerator.  The 'a-ha!' moment came only after I did a search for 'Ruby lazy evaluation'.

I've always believed that studying languages other than your 'main' one makes you a better programmer, but in this case I especially like how I learned how to use a language feature by seeing it in another language.

If you're curious about enumerators and generators here are a few links to check out:

[How to use an enumerator (Stack Overflow question)](https://stackoverflow.com/questions/20394909/how-to-use-an-enumerator)
[Ruby 2.0 Works Hard So You Can Be Lazy](http://patshaughnessy.net/2013/4/3/ruby-2-0-works-hard-so-you-can-be-lazy)


[What can you use Python generator functions for? (Stack Overflow question)](https://stackoverflow.com/questions/102535/what-can-you-use-python-generator-functions-for)
[Generator Tricks for Systems Programmers](http://www.dabeaz.com/generators/)
<br>

***
*Why, sometimes I've believed as many as six impossible things before breakfast.* 
-- The White Queen in *Through the Looking-Glass* by Lewis Carroll 

[^enumerable]: [Ruby 2.2.2 Enumerable docs](http://ruby-doc.org/core-2.2.2/Enumerable.html)
[^enumerator]: [Ruby 2.2.0 Enumerator docs](http://ruby-doc.org/core-2.2.0/Enumerator.html)
[^udacity]: [Design of Computer Programs course](https://www.udacity.com/course/design-of-computer-programs--cs212)
[^foot]: Lists, arrays, etc., *not* the Python collection module

