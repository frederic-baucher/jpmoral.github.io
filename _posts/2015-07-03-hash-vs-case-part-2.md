---
layout: post
title: "Hashes vs. case statements, Part 2"
date: 2015-07-03
categories: ruby
---

In the [last post]({% post_url 2015-06-29-fun-with-hashes %}) I wrote about how I implemented a Hash with regex keys and lambda values.  Here's the desired output:

- Strings beginning with 'Foo' => 'group A'
- Strings beginning with 'F' (but not 'Foo') => 'group B'
- Strings beginning with 'Bar' => 'group C'
- Strings beginning with 'Baz' => same string, but replace all 'a's with '0's

Implementing this using `case` is pretty straightforward:

```ruby
module StringLookup

  def self.lookup(string)
    case string
    when /^Foo.*$/ then 'group A'
    when /^F.*$/   then 'group B'
    when /^Bar.*$/ then 'group C'
    when /^Baz.*$/ then string.gsub('a', '0')
    end
  end

end
```

That's it.  No extra module, no mix-ins.  All the original specs still pass with this code.  This code is a bit more boring than the Hash code, but that's a good thing.  It's a lot simpler, easier to read and understand, and there's less to maintain.  

I didn't bother to profile the code since it wasn't necessary for what it was going to be used for.  Whatever performance advantage hashes could have offered, however, were probably negated by having to go over the keys to find a matching pattern.

All the code is in this [gist](https://gist.github.com/jpmoral/482a62b22ab2129be34c).  Note that to run the specs you will have to change the `require 'string_lookup'` and/or the method name in `StringLookup`.

<br/>

***
*"...she had never before seen a rabbit with either a waistcoat-pocket, or a watch to take out of it, and burning with curiosity, she ran across the field after it..."*
-- *Alice's Adventures in Wonderland* by Lewis Carroll

