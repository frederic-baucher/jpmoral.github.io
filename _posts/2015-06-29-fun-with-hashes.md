---
layout: post
title: "Hashes vs. case statements, Part 1"
date: 2015-06-29
categories: open_source
---
#### (or, sometimes fun isn't necessarily best)

A few weeks back I needed to write some code that would take a string as input and produce an output string according to rules similar to the following:

- Strings beginning with 'Foo' => 'group A'
- Strings beginning with 'F' (but not 'Foo') => 'group B'
- Strings beginning with 'Bar' => 'group C'
- Strings beginning with 'Baz' => same string, but replace all 'a's with '0's

The ruleset was small, and the number of input strings was not particularly large, maybe in the tens to low hundreds.  

Implementing this with a Hash, regex, and lambdas sounded like fun, so I did that.

Here are the specs:

{% highlight ruby %}
# string_lookup_spec.rb
require 'string_lookup'

describe StringLookup do
  subject { StringLookup }

  it "assigns strings beginning with 'Foo' to 'group A'" do
    expect(subject.lookup('Foobar')).to eq 'group A'
  end

  it "assigns strings beginning with 'F' but not 'Foo' to 'group A'" do
    expect(subject.lookup('Fbar')).to eq 'group B'
  end

  it "assigns strings beginning with 'Foo' to 'group A'" do
    expect(subject.lookup('Bar')).to eq 'group C'
  end

  it "strings beginning with 'Baz' have 'a's changed to '0's" do
    expect(subject.lookup('Bazaz')).to eq 'B0z0z'
  end
end
{% endhighlight %}

and the code:

{% highlight ruby %}
# string_lookup.rb
module RegexAndLambdaHash
  def value_for_key_matching(string)
    pattern = self.keys.find { |key| key =~ string }

    value_for(pattern, string)
  end

  def value_for(pattern_key, original_key)
    value = self[pattern_key]

    if value.respond_to? :call
      value.call(original_key)
    else
      value
    end
  end
end

module StringLookup
  LOOKUP = {
    /^Foo.*$/ => 'group A',
    /^F.*$/ => 'group B',
    /^Bar.*$/ => 'group C',
    /^Baz.*$/ => lambda { |x| x.gsub('a','0') },
  }.extend RegexAndLambdaHash

  def self.lookup(string)
    LOOKUP.value_for_key_matching(string)
  end
end
{% endhighlight %}

Let's see what this does.  `LOOKUP` is defined with regexes as keys and the desired strings as values.  `RegexAndLambdaHash#value_for_key_matching` allows strings to be matched to the regex keys.  `#value_for` returns the corresponding hash value or calls the lambda.

This code was fun to write, but there are some problems.

- `#value_for_key_matching` can't handle string keys
- `RegexAndLambdaHash` will have to be maintained
- for Ruby versions older than 1.9 `LOOKUP` will have to be an `ActiveSupport::OrderedHash`.  This is because prior to 1.9 hashes were unordered

In the next post let's see if a `case` statement works better.

