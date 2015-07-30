---
layout: post
title: "Anatomy of an Open Source Pull Request"
date: 2015-07-11
categories: ruby
comments: true
---

One of the great things about coding is the availability of a wide-range of open source libraries and plugins.  Ruby, for example, has a vibrant gem ecosystem that helps programmers focus on the problem domain and makes programming more fun.

Since early in my programming career I'd always wanted to contribute to open source but didn't know where or how to start.  There's already a lot of good advice about that on the Internet so I'll try to do something different here.  In this post I'll take one of my own pull requests and break it down from inception to merge.

#### The Project: LightService

[LightService](https://github.com/adomokos/light-service) is a gem that allows you to organize your code into a series of classes that perform a single action.  It's great for organizing and simplifying code that performs sequential tasks.  

We use it a lot at work, which goes to the 'scratch an itch' advice given to people who want to contribute to open source.  It's much easier code-wise and motivation-wise to add features and fix bugs on a project you already know and use.


#### The Feature Request

Use of the gem involves three modules: an `Action` takes inputs and does a single action with them.  These inputs are passed to the action via a `Context`, which is a subclass of `Hash`.  `Actions` are grouped together in an `Organizer`, which sets the initial context and runs the actions sequentially.

An `Action` may specify (via the `expects` macro) what keys it needs to be in the context to be able to do its work.  If the key is not found the action raises an error.

The [requested feature](https://github.com/adomokos/light-service/issues/67) is to allow aliases for keys in the context.  This would be useful for making actions more generic and being able to reuse them later with other actions.

#### Initial Design and Pull Request

In the issue discussion linked above I posted what I felt was an improvement on the original request.  Based on those ideas I wrote some code and opened a [pull request](https://github.com/adomokos/light-service/pull/68).  (Commits have since been added and the pull request closed.  See [the original diff](https://github.com/adomokos/light-service/compare/cb67e29cae...af185bee57b).)

The project owner [Attila Domokos](https://github.com/adomokos) came back with some suggestions to make the implementation cleaner.

#### Design Tweaks, Improved Pull Request, and Merge

The main suggestion was to limit the number of modules changed.  I had made changes to three modules, and the code could use some tightening.  At first I was reluctant to make changes, since they would involve having to override the `[]` and `fetch` methods, which `Context` inherited from the core `Hash` class.

After more discussion and code suggestions back-and-forth (see the pull request thread) I realized that the benefits outweighed my concerns.  `Context` was a subclass after all, the behavior of `Hash` itself would be unchanged, so I made the changes.

The final commit is [here](https://github.com/jpmoral/light-service/commit/4aa0cfa102ee16372f408ef22c2c9b6c2f8ae449).  I had to create a new branch and pull request because for some reason Git thought that the branch was up to date.  [This](https://github.com/adomokos/light-service/pull/69) is the pull request that actually got merged.

#### After the Merge

Of course things don't end with getting your changes merged.  While there is no legal obligation for you to support your changes (if they cause bugs, for example) it is good citizenship to do so.  Also, if you use the project and benefit from it, it is practical to help keep it in good working order.

My pull request was merged recently so it remains to be seen what fixes, if any, will be required.  I included the changes in a few of my own projects and it looks good.  Stuff still works and nothing broke.

#### Key Takeaways

- It's easier to start contributing to a project you're familiar with.  It's easier to find an itch to scratch, even though it might not be your own.
- Think about the changes you want to make before writing any code, but don't over-engineer your design either.
- Be ready to accept constructive criticism.  Discussion helps produce better code and makes everyone a better programmer.

It might be hard for some people to find a project and put themselves and their code out there, but I hope this helps someone to start contributing.


<br/>

***
*Oh, how I wish I could shut up like a telescope! I think I could, if I only knew how to begin.* 
-- Alice in *Alice's Adventures in Wonderland* by Lewis Carroll
