---
layout: slide
title: "Composition in Ruby: extend vs. include"
date: 2015-07-23
categories: ruby
theme: league
transition: slide
---
<section data-markdown>
##Composition in Ruby
###extend vs. include
</section>
<section data-markdown>
module behavior can be *mixed-in* to other objects
</section>
<section data-markdown>
###`extend`

- Defined on `Object` (including `Module` and `Class`!)

###`include`

- Defined on `Module`

(remember, Classes are Modules too)
</section>
<section>
    <section data-markdown>
        <pre><code>
```ruby
module Motion
  def default_movement
    "walking"
    end
  end
end

module Flight
  extend Motion
  def movement
    "flying"
  end
end

class Person
  extend  Motion
  include Speech
end

class Bird
  include Flight
end
```
        </code></pre>
    </section>
    <section data-markdown>
        <pre><code>
```ruby
RSpec.describe "Ruby composition methods" do
  describe "extend" do
    context "when a class extends a module" do
      it "adds the module methods to the class's class methods" do
        expect(Person.default_movement).to eq("walking")
      end

      it "does not add the module methods to the class's instance methods" do
        person = Person.new

        expect { person.default_movement }.to raise_error(NoMethodError)
      end
    end

    context "when a module extends another module" do
      it "adds the other module's methods to the original module's class methods" do
        bird = Bird.new

        expect(bird.movement).to eq("flying")
        expect { bird.default_movement }.to raise_error(NoMethodError)
      end
    end

    context "when an object extends a module" do
      it "gains the modules methods" do
        cat = Cat.new

        cat.extend(Speech)
        expect(cat.greet).to eq("Hello World")
      end
    end
  end
end
```
        </code></pre>
    </section>
</section>
<section>
    <section data-markdown>
        <pre><code>
```ruby
module Motion
  def default_movement
    "walking"
  end
end

module Swimming
  include Motion

  def movement
    "swimming"
  end
end

class Person
  extend  Motion
  include Speech
end

class Fish
  include Swimming
end

class Cat; end
```
        </code></pre>
    </section>
    <section data-markdown>
        <pre><code>
```ruby
RSpec.describe "Ruby composition methods" do
  describe "include" do
    context "when a class includes a module" do
      it "add the module methods to the class's instance methods" do
        person = Person.new

        expect(person.greet).to eq("Hello World")
      end
    end

    context "when a module includes another module" do
      it "adds the second module's methods to the original module's instance methods" do
        fish = Fish.new

        expect(fish.movement).to eq("swimming")
        expect(fish.default_movement).to eq("walking")
      end
    end

    it "is not defined on non-module objects" do
      cat = Cat.new

      expect { cat.include }.to raise_error(NoMethodError)
    end
  end
end
```
        </code></pre>
    </section>
</section>
<section data-markdown>
###Questions
####and hopefully, answers</h4>
Check out my blog:
jpmoral.com
</section>
