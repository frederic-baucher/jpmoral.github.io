---
layout: post
title: "Hosting Reveal.js Slide Decks on a Jekyll-generated blog"
date: 2015-07-29
categories: blogging
comments: true
---

## The Why

A couple of months ago I was invited by a student organization to give a career talk at the University of the Philippines.  I've never liked using presentation software (Powerpoint/Keynote/Impress, etc.) so I looked around for an alternative.  That's how I found [Reveal.js](http://lab.hakim.se/reveal-js/) which is, according to its description,

>A framework for easily creating beautiful presentations using HTML.

Indeed.  There are three things I like about Reveal.js:

- it allows me to work in a familiar environment.  Slides are created using HTML, CSS, Javascript, and Markdown.
- it doesn't require special software: just a text editor for writing and a browser for viewing.
- if the slides are uploaded they can be presented from anywhere with Internet access and a browser.

That last point was especially driven home when I forgot to bring a display output adapter to the talk mentioned above.  Unfortunately, I hadn't uploaded the slides, so I had to copy the Reveal.js library along with the presentation HTML to one of the organizer's machines.  Embarassing.

Last week I gave a short talk at the local Ruby meetup and of course created the slides with Revealjs.  After the earlier episode I decided that since I already had a blog (read: static site generated with [Jekyll](http://jekyllrb.com/) hosted on a GitHub page) I'd host the slides and present them from here.  I was able to do that, but the post with the slides were mixed in (no pun intended) with the other posts.  After some Googling I got all the regular posts on one page, and the slides on another.

## The How

Here's a high-level overview, this assumes familiarity with Jekyll and GitHub pages:

1. Copy Reveal.js to the site's root directory
2. Create a layout for slides
3. Create a post containing the slides (at this point this post will show up with the other, regular posts)
4. Create a separate page for presentations
5. Include only regular posts on the main page, and only presentations on the presentations page

### Steps 1-3
Steps 1 through 3 are nicely detailed by GitHub user @luugiathuy in [this presentation](http://luugiathuy.com/slides/jekyll-create-slides-with-revealjs/#/) which appropriately seems to have been created with Revealjs and hosted on a Jekyll blog.

The only thing I did differently was to remove everything outside of the `css`, `js`, `lib`, and `plugin` directories.  The steps are repeated here for completeness:

First, copy or git clone Reveal into your site's root directory:
```
git clone https://github.com/hakimel/reveal.js.git
```

or

```
git submodule add https://github.com/hakimel/reveal.js.git
```

Next create a `slide.html` in the `layout` directory.  Contents of that file are [here](https://github.com/jpmoral/jpmoral.github.io/blob/master/_layouts/slide.html) (link goes to my repo instead of @luugiathuy's gist).

Finally, create your slides in markdown!  Don't forget to include the following in the front matter:

```
layout: slide
```


### Step 4: Create a separate page for presentations

First decide the name for the page you want to host your presentations.  I named mine "slide decks".  Next, create a directory with that name, and an `index.html` in that directory: `slide decks/index.html`.

Jekyll will automatically add a link to `/slide decks` on your blog.  [See more about Jekyll pages](http://jekyllrb.com/docs/pages/).

### Step 5: Regular posts on main page, presentations on presentation page

At this point the main page still lists the presentations along with the regular posts, and the presentation page is empty.

If you have a vanilla Jekyll blog your `index.html` contains code like this:

{% highlight html linenos=table %}
{% raw %}<ul class="post-list">
  {% for post in site.posts %}
    <li>
      <span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span>
        <h2>
          <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title}}</a>
        </h2>
    </li>
  {% endfor %}
</ul>{% endraw %}
{% endhighlight %}

Assuming you add a "presentation" tag to your presentations, the following will only display regular posts:

{% highlight html linenos=table %}
{% raw %}<ul class="post-list">
  {% for post in site.posts %}
    {% if post.categories contains "presentation" %}
      <!--  don't show presentations -->
    {% else %}
      <li>
        <span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span>
          <h2>
            <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title}}</a>
          </h2>
      </li>
    {% endif %}
  {% endfor %}
</ul>{% endraw %}
{% endhighlight %}

The difference is that we are telling Jekyll not to display anything if a post's categories includes "presentation".

Next, display a list of presentations on the presentation page.  In the `index.html` created in step 4:

{% highlight html linenos=table %}
{% raw %}---
layout: default
title: Presentations
---
<div class="home">
  <h1 class="page-heading">Presentations</h1>
  <ul class="post-list">
    {% for post in site.categories.presentation %}
      <li>
        <span class="post-meta">{{ post.date  | date: "%b %-d, %Y" }}
        <h2>
          <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
        </h2>
      </li>
    {% endfor %}
  </ul>
  <p class="rss-subscribe">subscribe <a href="{{ "/feed.xml" | prepend: site.baseurl }}">via RSS</a></p>
</div>{% endraw %}
{% endhighlight %}

This is almost identical to the main index, except instead of `site.posts` we use `site.categories.presentation`, which is a Jekyll variable that lists all posts with a category of "presentation".

That's it!  You can now host and present your slides from your blog.

<br/>

***
*'Give your evidence,' said the King; 'and don't be nervous, or I'll have you executed on the spot.'* 
-- *Alice's Adventures in Wonderland* by Lewis Carroll
