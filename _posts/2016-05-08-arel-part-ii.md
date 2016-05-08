---
layout: post
title: Arel Part II — CTE Boogaloo
date: 2016-05-08 6:19:00
published: true
comments: true
tags: code, ruby, rails, arel
---

*This is the second entry in a three post series on a few intermediate Arel
recipes that I've used in my professional life and is adapted from my posts on
the SciMed blog, which can be found [here](http://scimedsolutions.com/articles).
For an introduction to this series—as well as some personal background—check out
[my previous post](/2016/04/27/background-and-arel.html).*

I was heading up a project about a year ago when a new requirement came through
from our client: some of their biostatisticians wanted an easy way to query data
that was spread across the majority of the tables in our database. Since they
didn't know our architecture terribly well, we decided the best thing that we
could provide them was a one-stop-shop database view that collated information
from all the tables that were relevant to their request.

For anyone new enough to database design to have never used a view, they're
incredibly useful. You can define a view similarly to a table, but, unlike a
table, it's built up of columns and data that actually live elsewhere--whether
that be a table or even another view. Since you just have to write the query to
define your view once, it makes querying data that originates from multiple
locations a cinch since you can just write a simple `SELECT` without any
complicated `JOIN` or `WHERE` statements. Some databases allow data to be
updated via the view itself, though I much prefer keeping views read-only and
updating the rows in the original tables for consistency and clarity. If you
don't want your view to be calculated on the fly for performance purposes,
certain databases even allow you to materialize your views and only refresh them
when desired. Read more about views
[here](http://www.mysqltutorial.org/introduction-sql-views.aspx) and
materialized views
[here](http://stackoverflow.com/questions/93539/what-is-the-difference-between-views-and-materialized-views-in-oracle)
(the answer by Mike McAllister is currently my favorite).

After writing the first draft of the database view for the client's
biostatisticians, it was time to test it out. After playing around with it for a
while, we discovered that, while the data was all there, the performance
degraded rapidly as our dataset increased in size. With the help of one of my
colleagues, we rewrote the view to use common table expressions (CTEs) and
bettered our performance by several orders of magnitude.

If you're relatively unfamiliar with CTEs, you can think of them as a sort of
named, temporary table built by querying existing artifacts in your database.
They're quite similar to views themselves with the notable exception of only
existing within the scope of the current query. If you're curious, you can read
more about them
[here](https://hashrocket.com/blog/posts/understanding-common-table-expressions-with-fizzbuzz)
or
[here](https://en.wikipedia.org/wiki/Hierarchical_and_recursive_queries_in_SQL#Common_table_expression).

While we didn't use Arel to construct the aforementioned view, it provided a
great example of the power behind CTEs. Even though ActiveRecord doesn't give us
CTE support out of the box, we can craft our own CTEs with Arel (or you can use
the
[postgres_ext](https://dockyard.com/blog/2013/09/06/postgres_ext-adds-rank-and-common-table-expressions)
gem if you're using Postgres).

For the purposes of this exercise, imagine that we have the following models:

~~~ruby
class Recipe < ActiveRecord::Base
  belongs_to: :author
end

class Author < ActiveRecord::Base
  has_many :recipes
end
~~~

Both models have a primary key of `id`, as usual. The `recipes` table also has
an integer column called `rating` that represents the quality of the recipe as
well as the standard `author_id` foreign key. For this exercise, our CTE will
represent recipes whose rating is between 60 and 80.

To start off, we need to instantiate a new `Arel::Table`. We'll pass in a symbol
representing the name that we want our CTE to be referenced by in our final
query as the only argument.

~~~ruby
cte_table = Arel::Table.new(:recipes_rated_between_60_and_80)

~~~

Now, we have to create the definition of our CTE.

~~~ruby
recipes = Recipe.arel_table
cte_definition = recipes
                   .project(recipes[:id], recipes[:author_id])
                   .where(recipes[:rating].between(60..80))
~~~

Lastly, we need to combine our `cte_table` and our `cte_definition` in an
`Arel::Nodes::As` instance.

~~~ruby
composed_cte = Arel::Nodes::As.new(cte_table, cte_definition)
~~~

We can now use our CTE in other queries. For example, if we wanted to grab all
the authors who have written a recipe with a rating between 60 and 80, we could
do the following:

~~~ruby
authors = Author.arel_table
author_ids = authors
              .project(authors[:id])
              .join(cte_table).on(authors[:id].eq(cte_table[:author_id]))
              .with(composed_cte)

Author.where(authors[:id].in(author_ids))
~~~

This leads to Rails executing the following SQL query:

~~~sql
SELECT "AUTHORS".*
FROM "AUTHORS"
WHERE "AUTHORS"."ID" IN (
  WITH "RECIPES_RATED_BETWEEN_60_AND_80" AS (
    SELECT "RECIPES"."ID", "RECIPES"."AUTHOR_ID"
    FROM "RECIPES"
    WHERE "RECIPES"."RATING"
    BETWEEN 60 AND 80
  )
  SELECT "AUTHORS"."ID"
  FROM "AUTHORS"
  INNER JOIN "RECIPES_RATED_BETWEEN_60_AND_80"
          ON "RECIPES_RATED_BETWEEN_60_AND_80"."AUTHOR_ID" = "AUTHORS"."ID"
)
~~~

Thanks to ActiveRecord and Arel, we now have an `ActiveRecord::Relation` of
`authors` that fulfill our specified conditions.
