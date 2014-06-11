---
title: Day 2 - The Blog
layout: post
comments: true
---
Though I got started making this blog yesterday, I got the chance to devote a lot more time to it today. I had managed to get the basic functionality done yesterday afternoon in between orientation meetings, so I started off the day working on the aesthetic. After tinkering with the layout and color scheme for a few hours, I decided to add a comment feature, so I spent some time looking at [Disqus](http://disqus.com/), [IntenseDebate](https://www.intensedebate.com/) and [Juvia](https://github.com/phusion/juvia). While I really appreciated that Juvia was open source, I was turned off by the fact that it doesn't seem well supported---several of the most recent commit messages reference starting to upgrade for Rails 3.2 rather than the current 4.1. As a result, I ended up going for Disqus.

Once I decided on Disqus, I considered using [Octopress](http://octopress.org/), a Jekyll framework, to tie it into what I'd already written. After tinkering around with it for a while, though, I ended up deciding against it; it didn't seem like it would really add much of anything to my blog (at least at the moment) aside from a little extra bloat. Plus, it turned out that setting up Disqus with Jekyll is [incredibly easy](https://help.disqus.com/customer/portal/articles/472138-jekyll-installation-instructions).

To finish off the day, I added a link to an RSS feed of my blog's five most recent posts. Since I'm no XML pro, and I've never used RSS---as either a developer or even as a reader---I ended up using the directions found [here](http://joelglovier.com/writing/rss-for-jekyll/) by Joel Glovier and [here](https://github.com/snaptortoise/jekyll-rss-feeds) by snaptortoise, making the requisite edit to show the posts from the last week (in order words, the last five posts).

Tomorrow, I may work on the "improve searchability" task that Patrick put up on Basecamp and/or work on making my layout a little more mobile-friendly--the layout works on mobile/small browser windows thanks to Bootstrap, but it could still use some tinkering. Regardless of what I do with the blog, I'd definitely like to dive into some Rails, so I'll probably start on some of the basics for the Hacker News clone.
