---
layout: layouts/post.njk
title: Bulletproof Craft Components
metaDesc: >-
  Here's a frontend development method that I've found while building large,  
  complex Craft sites.
date: 2019-07-23T21:53:40.169Z
tags:
  - twig
  - components
  - markup
  - frontend
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
{# _components/hero.twig #}

<div>
    <span>{{ entry.title }}</span>
    <p>{{ entry.textContent }}</p>

    {% set cta = entry.cta.one %}

    {% if cta %}
        <a href="{{ cta.buttonUrl }}">
            {{ cta.buttonText }}
        </a>
    {% endif %}
</div>

{# articles/index.twig #}

{% include '_components/hero' with { entry: entry } %}
```

You've probably seen components written like this before, too. And there's no immediate issue with this approach—it'll render perfectly on the page while it's being written, and works beautifully with the CMS.

### an issue arises

Let's say you've built the above component rather early in the project, and a few weeks/months later you need to use that Hero component for a new page that's just been approved.

You create the new page in Craft and add content, and write the markup/CSS for the 'unique' stuff on the page—perhaps a custom map on this page, or a one-off interactive tool of some sort. The new page calls for a Hero, but no problem, right? Just use the existing Hero component and you're good to go!

But wait, this new content you're adding doesn't have the `cta` or `textContent` fields. You want to use the `textSnippet` and `button` fields.

Because your component *assumes* stuff about the data it's rendering, you can't use it. You'll have to update all the uses of the Hero component or make a new component to account for this new data structure.

As a result, components usually bloat in both number and complexity towards the end of most projects.

## The Solution

I'd humbly submit that there's a slightly better approach to how your write components in Craft. Instead of relying on certain fields/data structures to be present in your content, write your components in the thinnest, bare fashion you can. Here's an example of the Hero component from earlier rewritten:

```twig
{# _components/hero.twig #}

<div>
    <span>{{ title }}</span>
    <p>{{ text }}</p>

    {% if buttonUrl %}
        <a href="{{ buttonUrl }}">
            {{ buttonText }}
        </a>
    {% endif %}
</div>

{# articles/index.twig #}

{# Import all components under a single Macro #}
{% import '_components/macros' as createComponent %}

{# Render a single Hero component with the provided data #}
{{ createComponent.hero({
    title: block.heading,
    text: block.textContent,
    buttonText: block.cta.one.buttonText ?? null,
    buttonUrl: block.cta.one.buttonUrl ?? null
}) }}
```

It's a subtle difference, but an important one. See how the new version doesn't assume we have a `cta` field, `textContent` field, or&hellip; really anything at all. We're just rendering the actual data being passed, and that's it.

Yes, there are more variables being passed. Of course, it still requires the same two files. Yep, it will make your 'Page Builder' field a bit more verbose to write.

However, it makes your components *truly* reusable, wherever you need them. They're now *explcitly* frontend concepts, instead of attempting to bridge the gap between the CMS fields and the component's data.

That's essentially the "secret" behind truly bulletproof, reusable components:

**Write your components as thin as (reasonably) possible, assuming nothing about the data structures being passed.**
