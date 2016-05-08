---
layout: post
title: Arel Part I — The Searchening
date: 2016-05-01 5:58:00
published: true
comments: true
excerpt: In recent years, the developer community has seen an increased focus on providing good user experience...
tags: code, ruby, rails, arel
---

*This is the first entry in a three post series on a few intermediate Arel
recipes that I've used in my professional life and is adapted from my posts on
the SciMed blog, which can be found [here](http://scimedsolutions.com/articles).
For an introduction to this series—as well as some personal background—check out
[my previous post](/2016/04/27/background-and-arel.html).*

In recent years, the developer community has seen an increased focus on
providing good user experience (UX). [Interesting
research](https://www.viget.com/articles/are-hollow-icons-really-harder-to-recognize-a-research-study)
has been conducted, and new tools have been created [to aid in
accessibility](http://khan.github.io/tota11y/).

Often, I find that UX discussions tend to be, understandably, focused on the
front-end over the back when it comes to web development. It's important to
remember, though, that back-end implementation details can improve a user's
experience independent of the operations being conducted within that user's
browser.

One feature that this is particularly relevant to is search. You can have myriad
search forms that are exactly the same on the front-end that function incredibly
differently: they can use the exact same markup and styling and send the exact
same HTTP request back to a server but receive very different responses. On the
back-end, a search can be case-sensitive or fuzzy, or it can even have a given
string split into multiple `WHERE` conditions based on a delimiter or flag.

With Rails, we can handle a bunch of simpler searches out of the box with
ActiveRecord; for some of the less trivial situations, we can use a search gem
like [Ransack](https://github.com/activerecord-hackery/ransack).

If we don't want to add another dependency to our project, our
current search tool doesn't support the behavior that we want or we just feel
like implementing search ourselves, we can use Arel. In this post, we'll cover
building out a case-insensitive search that returns results with partial matches
in a `string` or `text` column. If it doesn't conflict with your project's
requirements, such a search can provide a lot of flexibility to your users,
bettering their experience on your site.

For anyone that's only used ActiveRecord by itself, the `where` method may
immediately come to mind as a possible first step for building out our search.
That line of thinking will likely lead to a rough draft of the following form if
we're attempting to search on an `Article` model:

~~~ruby
Article.where(title: target_title)
~~~

Depending on your database configuration, this may only do case-sensitive, exact
matches. If your user just remembers one of the words in the article title
they're looking for—for example, *Rails*—they might not get back *Advanced Rails
Topics* or even *RAILS*. Luckily, Arel gives us all we need to ensure our
intended behavior with the following query:

~~~ruby
Article.where(
  Article.arel_table[:title]
    .lower
    .matches("%#{target_title.downcase}%")
)
~~~

If the user passes in *Rails* in the title search field, this will generate the
following SQL:

~~~sql
SELECT "ARTICLES".*
FROM "ARTICLES"
WHERE (LOWER("ARTICLES"."TITLE") LIKE '%rails%')
~~~

This will correctly give you back any results that contain the string
"Rails"—or any case variant—within their title. Do make sure that, if the
column you're searching on can contain the `%` character, your test suite checks
to make sure the behavior is as you (and your users!) intend.
