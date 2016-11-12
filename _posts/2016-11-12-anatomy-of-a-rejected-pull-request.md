---
layout: post
title: "Anatomy of a (Rejected) Open Source Pull Request"
date: 2016-11-12
categories: open-source
comments: true
---

A few posts back (over a year ago!) I wrote about getting a pull request merged into an open-source project.  A lot of times though, pull requests get rejected.  Let's take a look at another of my pull requests and see why it didn't get merged.

#### The Project: LightService

I talked about [LightService](https://github.com/adomokos/light-service) in the previous pull request post.  To recap, it's a gem that makes it easy to organize code that consists of a series of distinct steps.

#### The Pull Request

I created a [pull request](https://github.com/adomokos/light-service/pull/72) to address surprising behavior (to me, at least) around LightService actions and inheritance.  Let me illustrate with some code.  Familiariaty with how LightService works isn't necessary; this is just going to be a high-level overview.  Consider the following:

{% highlight ruby %}
class ActionClass
  extend LightService::Action

  expects :some_expectation
  promises :some_promise
end

class ActionSubclass < ActionClass; end
{% endhighlight %}

What the `expects` and `promises` macros do is to set certain class instance variables on `ActionClass`.  These are not inherited by `ActionSubclass` since these are instance variables on the class object, not class variables.

Which brings us to the pull request.  I wanted `ActionSubclass` to have the same expectations and promises as `ActionClass`.  The benefit would be to define expectations and promises once on a single class while allowing subclasses that process them differently.

#### Discussion and Rejection

The discussion is pretty straightforward.  There are two reasons this didn't get merged:

1. I wasn't too keen on it myself.  I found an alternative approach that solved the issue I was trying to address with inheritance.  This left the pull request with no proponents and no purpose.

2. The pull request went against the project's design philosophy.  The project owner stated

>The reason I opted to use class methods for the actions was to limit inheritance. With inheritance there is an "inferred" state that I tried to avoid.

Clearly this pull request was doomed from the start.

#### Key Takeaways

- Pull requests need to solve problems.  No problem, no merge.
- Understand a project's design before writing any code.  If in doubt ask the maintainers or other users.
- Sometimes pull requests get rejected, it's all part of contributing to open source.


<br/>

***
“In some cases, we learn more by looking for the answer to a question and not finding it than we do from learning the answer itself."
-- Dallben in *The Book of Three* by Lloyd Alexander

