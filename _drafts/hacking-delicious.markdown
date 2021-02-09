---
layout: post
title:  "Hacking delicious"
date:   2020-06-25 20:33:52 -0700
categories: bookmarks book
---



I first started this blog for people using **[Jekyll](https://jekyllrb.com/)**,
a static web generator, and who may be
**[pinboard.in](https://pinboard.in)** users too.
Along the way, I will try to incorporate some
of the examples from the book *Hacking del.icio.us* by Leslie M. Orchard, 2006,
adapted for **Pinboard** bookmark manager instead of **del.icio.us**,
as well as other types of JSON feeds.

A lot has changed since 2006, some of the tools and
concepts no longer apply and must be reworked in new ways.
When *Hacking del.icio.us*
was written, most open-source web development used the LAMP stack
(Linux, Apache, MySQL, and PHP).
Nowadays, there is a lot of attention
given to the JAM stack (Javascript, APIs, and Markup).
Jekyll fits in the JAM stack very well, producing static markup.
The CSS examples will probably be changed the least.
JavaScript is much improved and more widely used, so these examples may need
special attention.
Finally, we will have to adapt to the APIs available today.

### The book about bookmarking ###




The book *Hacking del.icio.us* was divided into three parts, proceeding
from an overview of the bookmarking service, to programming examples,
and the last part covering alternatives. These can serve as basic 'categories'
which group blog posts together in Jekyll.

	Part I 		Exploring del.icio.us     overview
	Part II 	Remixing del.icio.us      development
	Part III 	Beyond del.icio.us        alternatives

When a blog post focuses on a subject or particular example from a chapter
in one of these parts, it will be assigned to the category associated with
that part of the book.
These blog posts will skip around through different parts of the book in no
particular order.

For instance, a blog post about **Pinboard** pages available to the public
without an account would correspond to chapter 1 *"What Is del.icio.us"*, so
this blog post would be assigned to the "overview" category.

### Source Code ###

As this blog trudges through different examples using the **Pinboard** API and JSON feeds, I will add corresponding files to this repository.

  [https://github.com/rheotaxis/loving-pinboard](https://github.com/rheotaxis/loving-pinboard)

The sample code discussed in blog posts
may start with code originally made available with the book *Hacking del.icio.us*,
but will be reworked for **Pinboard** and other APIs available now.
