---
layout: post
title: Day 3 - Finishing the blog and moving on
comments: true
description: Today I finished up my blog for the time being and checked out some Vim plugins.
---
Today, I put some finishing touches on my blog, which included making a custom 404 page (see [here](http://andycandrea.github.io/404.html)) and adding a sitemap. Knowing that Github Pages runs Jekyll using the `--safe` flag, which prevents Jekyll plugins from running, I originally looked into generating the sitemap myself. However, I soon discovered that [Jekyll-sitemap](https://github.com/jekyll/jekyll-sitemap) is one of the [four plugins currently supported](https://help.github.com/articles/using-jekyll-plugins-with-github-pages) by GitHub Pages. It also happens to be very [easy to use](https://help.github.com/articles/sitemaps-for-github-pages).

After making the above improvements, I decided to look into other blogs that use Jekyll for inspiration. From the Jekyll wiki, I came across one in particular that seemed particularly robust, which can be found [here](http://rsms.me/) (source [here](https://github.com/rsms/rsms.github.com)). While I don't plan to make my blog terribly similar to this, I may revisit it whenever I next feel like tinkering. I also happened across the [Masteruby](http://masteruby.github.io/) blog, which had a really interesting post detailing several Vim plugins that are useful for working in Ruby---check it out [here](http://masteruby.github.io/productivity-booster/2014/05/02/vim-plugins-for-ruby.html#.U5mpgZRdWwF). One quick note: for anyone planning to use the vim-ruby plugin---which has a feature to automatically indents lines within conditionals, loops, defs, etc---it's useful to set the indentation to two spaces per Ruby convention. To do so, you can throw the following into your `.vimrc` file:

    set expandtab
    set tabstop=2 shiftwidth=2 softtabstop=2
    set autoindent

While installing the plugins I picked out from the Masteruby post, I was doing some research and talking to the other interns via Campfire about the different projects we're considering working on this summer. We still haven't come to a consensus about what we'll be doing, but all of the projects that we seem to be leaning towards look pretty interesting. Hopefully, I'll have something more conclusive to report in my next post.
