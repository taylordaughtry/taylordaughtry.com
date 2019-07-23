---
layout: layouts/post.njk
title: Bulletproof Craft Components
date: 2019-07-23T20:55:49.639Z
tags:
  - twig
  - components
  - markup
  - architecture
---
The rise of Atomic Design is one of my favorite developments in modern websites. It's made design & development faster, and made the average CMS more usable for clients. Building sites as a series of discrete components makes everybody's lives *far* more enjoyable—personally I *love* watching sites come together exponentially faster as components are built out.

However, I'd humbly submit that many implementations of Atomic Design have an issue. It's something that affects the speed of future maintenance, and it's not easily-fixed once you've started using the approach in your project. I'd argue it negatively affects the entire project as a whole.

That problem is this:

**Your components might not be as reusable as you think.**

## The Core Issue

One of the main benefits of components is the fact that you're able to reuse them across many different templates. Thanks to the [DRY principle]() means it takes less time to build the project.

You examine the design (or pair up with a designer) and divide the project into a variety of components. After taking a look, you start building out sliders, heroes, grids, the ever-present media/card component—you get the picture.

As you're doing this, you also tie each component into the CMS in some fashion. For [Craft](), that means writing your components in Twig and creating a 'Page Builder' field, or something similar. You'll loop through the various matrix blocks/assets/entries and so forth, and output the various data your component needs to render properly.

**However.**

As you get closer to the end of the project, you begin finding a problem when you try to reuse your fields:

*your components *assume* things about the data they're rendering.*


## An Example

Here's an example of a recent component I've seen in the wild:

```twig
{% test value %}
```
