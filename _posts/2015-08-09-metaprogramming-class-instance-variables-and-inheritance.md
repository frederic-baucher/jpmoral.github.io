---
layout: post
title: "Class instance variables and inheritance"
date: 2015-08-09
categories: ruby
comments: true
---

Let's dive into some code:

{% highlight ruby %}
module Echoer
  def echoes(*phrases)
    @phrases.concat(phrases)

    @phrases.each do |phrase|
      next if self.respond_to?(phrase.to_sym)
      define_method("#{phrase}") { phrase.to_s }
    end
  end

  def known_phrases
    @phrases.map do |phrase|
      phrase.to_s
    end
  end

  def self.extended(mod)
    mod.instance_variable_set(:@phrases, [])
    mod.send :include, InstanceMethods
  end

  module InstanceMethods
    def echo(phrase)
      phrase.to_s
    end

    def known_phrases
      self.class.known_phrases
    end
  end
end

class Person
  extend Echoer
  echoes :hello, :goodbye
end
{% endhighlight %}

Here we've defined a module `Echoer` and a class `Person` that `extends`s it.  To understand what this code does let's try a few examples:

{% highlight ruby %}
Person.known_phrases # => ["hello", "goodbye"]

p = Person.new

p.known_phrases # => ["hello", "goodbye"]
p.echo("a phrase") #=> "a phrase"
p.echo([1, 2, 3]) #=> "[1, 2, 3]"

p.hello # => "hello"
p.goodbye # => "goodbye"
{% endhighlight %}

We see that `Echoer` adds the following behavior to a class that extends it:

- the class gains the `echoes` method which defines what instances of the class know how to 'say'.
- the class and instances of it may be queried about what phrases they know via the `known_phrases` method
- instances of the class gain the `echo` method which returns a string version of its argument

Thus `Person`s know how to say "hello" and "goodbye", as well as arbitrary phrases through `echo`.  If we forget, we can also ask `Person` and its instances what phrases it knows.

Now, let's try subclassing:

{% highlight ruby %}
class Shopper < Person
end

s = Shopper.new
s.hello # => "hello"
s.goodbye # => "goodbye"

s.known_phrases # => ["hello", "goodbye"]
{% endhighlight %}

So far, so good.  But wait, there's more:

{% highlight ruby %}
s.known_phrases # => NoMethodError: undefined method `map' for nil:NilClass
Shopper.known_phrases # => NoMethodError: undefined method `map' for nil:NilClass
Shopper.echoes :charge # => NoMethodError: undefined method `concat' for nil:NilClass
{% endhighlight %}

`Shopper`s know how to say "hello" and "goodbye", but they don't know that they know it!  Also, we can't add to what a `Shopper` can say.  What's going on?

When we defined `Shopper`, it inherited `hello` and `goodbye` from `Person`.  It did *not* inherit `@phrases`.  This is because `@phrases` is a class instance variable of `Person`.  It is only defined on that class.

Programmers who use the `Echoer` module may be surprised by the inheritance (non-)behavior of `echoes` and `known_phrases`, so let's fix it.

We simply need to add the following to `Echoer`:

{% highlight ruby %}
def inherited(subclass)
  subclass.instance_variable_set(:@known_phrases, @known_phrases)
  super(subclass)
end
{% endhighlight %}

Here we use the `inherited` hook to customize what happens when a class that extends `Echoer` is subclassed.  Interesting points are:

- the correct definition is `def inherited` not `def self.inherited`.  Using `def self.inherited` will not add an `inherited` method to classes extending `Echoer`.
- `inherited` accepts the subclass as an argument.  We simply set the subclass's `@known_phrases` to the value of the superclass's `@known_phrases`.
- there's no call to `super`.  Things seem to work fine without it.  If anyone can point to an authoritative resource stating that `super` should or should not be called in `inherited`, I'd really appreciate it.

Now inheritance works as one might expect:

{% highlight ruby %}
Shopper.echoes :charge
Shopper.known_classes # => ["hello", "goodbye", "charge"]

s = Shopper.new
s.known_classes # => ["hello", "goodbye", "charge"]
s.charge # => "charge"
{% endhighlight %}

Full code is below.  Leave a comment!

{% highlight ruby %}
module Echoer
  def echoes(*phrases)
    @known_phrases.concat(phrases)

    @known_phrases.each do |phrase|
      next if self.respond_to?(phrase.to_sym)
      define_method("#{phrase}") { phrase.to_s }
    end
  end

  def known_phrases
    @known_phrases.map do |phrase|
      phrase.to_s
    end
  end

  def self.extended(mod)
    mod.instance_variable_set(:@known_phrases, [])
    mod.send :include, InstanceMethods
  end

  def inherited(subclass)
    subclass.instance_variable_set(:@known_phrases, @known_phrases)
  end

  module InstanceMethods
    def echo(phrase)
      phrase.to_s
    end

    def known_phrases
      self.class.known_phrases
    end
  end
end

class Person
  extend Echoer
  echoes :hello, :goodbye
end

class Shopper < Person
  echoes :charge
end
{% endhighlight %}

<br/>

***
*The more that you read, the more things you will know. The more that you learn, the more places you'll go.*
-- *I Can Read With My Eyes Shut!* by Lewis Carroll


