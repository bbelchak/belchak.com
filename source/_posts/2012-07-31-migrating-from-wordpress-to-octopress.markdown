---
layout: post
title: "Migrating from WordPress to Octopress"
date: 2012-07-31 17:22
comments: true
categories: blog
---

## WordPress is awesome, why'd you switch?
So, I've been really hating WordPress as a blog engine lately. Tons of spam,
lots of emails from spam accounts signing up and trying to post comments, etc.
We had some discussion at the office recently about what blog engine to use for
our corporate blog, (we ended up using WordPress) but through the process I decided
to evaluate [Octopress](http://octopress.org) as an approach to static content
serving for my personal blog.

On top of the security issues that I've been dealing with (constantly updating the
software, plugins and deleting spam), I host at [Dreamhost](http://dreamhost.com/).
They are not known as the fastest hosting company around, and I was finding that my
pages were loading so slowly that it was beginning to get frustrating.

Plus, I could never find a good WordPress theme that I wanted to look at and I never
have time to spend customizing a complex WordPress theme.

## Enter Octopress
I evaluated Octopress for about 10 minutes and knew I was hooked. I loved the
simplicity and the beauty of its default theme (and since it's themeable using
[SASS](http://sass-lang.com/) I can easily customize it) and the ease of deployment
to any number of hosting platforms (rsync, [github pages](http://pages.github.com),
[heroku](http://heroku.com), etc) leaves no excuse not to use it! Also, since all
the content is generated dynamically on your machine and then uploaded as static content,
it is very fast to serve on the server, and is less prone to spam and security holes.

## How I did it
I simply exported all of my posts and pages using the Export functionality in the WordPress
administration tool and then ran it through [exitwp](https://github.com/thomasf/exitwp/).
The result was phenomenal! I ended up with an export of all my pages and posts. Since
I was already using [Disqus](http://disqus.com) all the comments came over automatically.

I did have to do some very basic formatting for some of my code snippets in order to get
the syntax highlighting to work properly, but that was a simple matter of just setting the
language in the generated markdown files.

## Deploying
I use the Rsync deploy method to my Dreamhost shared server, but I am also considering
migrating to [github pages](http://pages.github.com/) at some point. Deploying is as simple as:

```
$ rake generate
$ rake deploy
```

And that's it! You can even streamline it more by only running one command:

```
$ rake gen_deploy
```

Overall, the migration was even more simple than I had expected, and I'm very happy with the
results! Now the hard part is coming up with interesting content to put here!
