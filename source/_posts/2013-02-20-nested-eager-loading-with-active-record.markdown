---
layout: post
title: "Nested Eager Loading with Active Record"
date: 2013-02-20 22:50
comments: true
categories: [rails, activerecord]
---

Given that you have used Rails for a medium sized project, or that you are zealous with your queries to the database, you've probably heard of `activerecord`'s `includes` method to eager load relations in order to prevent the N+1 query problem.

Don't fret if you haven't, I'll break it down for you. Imagine you have a `Post` model which may have one or more `Comment`, and that in your view you do something of the sort (assuming `@posts = Post.all`):

``` erb
...
<% @posts.each do |post| %>
  <article>
    <p><%= post.body %></p>

    <footer>
      <% post.comments.each do |comment|
        <p><%= comment.body %></p>
      <% end %>
    </footer>
  </article>
<% end %>
...
```

In this snippet, a query for fetching the comments is being performed for each post, so it's N for N equal to the number of posts plus 1 queries to list the posts. For a big number of posts you'll swamp your database.

In order to prevent this you can do `@posts = Post.includes(:comments).all` that will eager load all the comments of all the posts and store them in memory, so when you try to access them you don't hit the database.

### Nested Eager Loading

A feature that I find rather undocumented is that it is possible to eager load a relation of an object you're eager loading, such as the author of a comment if you wanted to access it's name or avatar, for example.

That can be achieved by passing an `Hash` as an argument to the `includes` method instead of a `Symbol`, for the example above it would be, `@posts = Post.includes(comments: :author).all`.

I have tried it to three levels of nesting which is already more than you should ever need, still it can go deeper than that, not sure how much deeper, though.
