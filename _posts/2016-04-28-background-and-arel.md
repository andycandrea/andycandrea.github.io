---
layout: post
title: A Little Background (and Introducing My Series on Arel!)
date: 2016-04-27 16:56:00
published: true
comments: true
tags: code, ruby, rails, arel
---

With the exception of building a basic HTML page back in middle school, my first
exposure to programming was during my sophomore year of college. At the time, I
was an astrophysics major. To satisfy one of the requirements for my degree, I
took a class called _Introduction to Numerical Techniques in Physics_, which had
been described to me as basically "programming for physicists". Since I've
always been interested in both computers and computing, I excitedly signed up
for the only 8AM class I'd ever take in college.

Once the semester started, I quickly came to realize that, even though the
course title included "Introduction" and there were no CS prereqs, this wasn't
going to teach me anything about programming. I struggled through the semester
and somehow ended up with a B even though I didn't even have a clue about such
advanced topics as the programmatical meanings of "loops" and "conditionals" by
the time finals rolled around.[^1]

The following summer, I signed up for an introductory programming class in the
CS department using the same language that I'd used in my physics class
(MATLAB). The difference between the two classes was, frankly, night and day. It
was amazing how much easier it was to learn when you start from the beginning
with a simple "Hello, world!" program instead of writing supernova simulations
and generating solutions to systems of differential equations from the get-go.
Now that I had a little more background and context, I fell in love with writing
code. By the time that the summer finished, I'd already decided to add a second
major in computer science.

Over the next two years, I dabbled in several programming languages—Java, C,
JavaScript, Python, MATLAB and IDL, if memory serves—but didn't feel extremely
comfortable in one. Luckily, I had one class that exposed me to relational
database design and SQL, which helped me to land [a fantastic
internship](https://www.viget.com/internships/rails-developer-intern),
where I started to develop the web dev skills that I'm now extremely proud of.

During my internship, I was exposed to my first
[ORM](http://stackoverflow.com/questions/1152299/what-is-an-object-relational-mapping-framework):
ActiveRecord. At first, I thought that it was the most wonderful tool that I'd
ever encountered—with a simple `save` or `update_attributes`, I could update my
data without ever having to write a single `INSERT`.

Since then, I've learned a lot. I now know that both Rails and ActiveRecord,
while great tools, have enough flaws to warrant their own lengthy blog posts.
Happily, I no longer shudder at the sound of the phrase "raw SQL"; in fact, I've
come to enjoy writing out queries, views and common table expressions manually.
When well-written, it tends to be quite readable and straight-forward, and
writing it out yourself can sometimes make performance gains easy to come by.

With all that said, I still avoid committing much raw SQL to this day. Why, you
ask? There are a few reasons, but I want to concentrate on one in particular:
future-proofing.

One of the great advantages of sticking with an ORM is generalization. If you
write raw SQL all over your Postgres-backed application only to discover that
your client needs you to use Oracle a few years down the road, you're going to
have to search through your entire codebase to figure out if anything is
incompatible in Oracle. Meanwhile, if you'd stuck with ActiveRecord, all you'd
need to do is swap out the adapter specified in your `database.yml`. Using
ActiveRecord also makes it much less painful if you need to connect to different
types of databases in different Rails environments—I've seen it happen—since
all of ActiveRecord queries will get automatically translated into the proper
dialect before they hit your database.

That said, ActiveRecord isn't perfect, and it's unfortunately not as
feature-complete as raw SQL when it comes to interacting with your database.
Luckily for those of us who tend to hit plenty of use cases too complicated for
ActiveRecord to handle—[writing software for the world's leading
minds](http://www.scimedsolutions.com/) tends to be non-trivial—Rails gives us
another option for keeping our database code generalized: Arel.

For those who have never used it before, [Arel](https://github.com/rails/arel)
is the [AST](https://en.wikipedia.org/wiki/Abstract_syntax_tree) manager used by
ActiveRecord; basically, it's the level below ActiveRecord that determines how
to construct the actual SQL string that's sent along to and executed by your
database. It also happens to let you write Ruby in a lot of places that
ActiveRecord would require a raw SQL string.

The power, flexibility and generalizability behind Arel prompted me to write a
short series of posts containing intermediate Arel recipes for the blog over at
[scimedsolutions.com](scimedsolutions.com/articles). You can check the originals
out there or read slightly modified versions here on
[andycandrea.github.io](andycandrea.github.io). Heck, you can even read both if
you so desire, though the content is quite similar.

-------------------------------------------------------------------------------

#### Footnotes

[^1]: I did, at least, learn a few fun [MATLAB Easter eggs](http://www.mathworks.com/matlabcentral/answers/2001-what-matlab-easter-eggs-do-you-know) in that class, though.
