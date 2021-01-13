---
title: "FinTS101 Ep. 4: Let there be color"
date: 2021-01-13T20:19:31+01:00
categories: ["fints101"]
tags: ["javascript", "project" ]
---

I’m trying to keep this post short so I skip the TL;DR intro section.

If you followed my posts, you’ll remeber that last time I finished building a
nested tag-based representation of the parse tree using a general visitor. I did
this in the hope that I could easily modify it to create html which can the
displayed according to tags, e.g. print a section in a color according to its
role in the grammar.

It turns out that this was about as easy as I hoped! 😄 Let’s see the result and
I’ll explain what happened:

![HIBNK in color](/img/fints101/fints101-hibnk-in-color.png)

You can see that

* every colon that separates “fields” is colored pinkish
* the *segmentkennung* part of the grammar got its own background color
* the numbers are all brown *but* the special case of the *segment number* also
  got a custom background.


I did a little detour (well, not really a detour when learning web development!)
visiting *(pun!)* html principles and discovered that styling my html based on
CSS could just be the right thing.

The [selector mechanism](https://www.w3schools.com/css/css_selectors.asp) and
the specificity that allows adding up style properties fits the nested tagged
syntax perfectly. I’m so happy to have discovered that! See In retrospective,
this should have been obvious because CSS was just made for typesetting
marked-up language elements (and more) that html is. Still astonished what a
perfect fit that is..

Anyway. What I did was:

* change the visitor to output html code with `<span class=“XX">` elements
  instead of pseudo-markup
* write a CSS
* change the web service to deliver html and the `style.css` file.


A sample line in the html looks like this

```html
<span class="segmentnummer"><span class="DT_num">1</span></span>
```

This one will turn the background color of the `segmentnummer` part blue while

```html
<span class="segmentversion"><span class="DT_num">1</span></span>
```

will have no custom background defined and will only apply the reddish color style for `DT_num`.

The CSS is pretty standard. Notice the `.segmentnummer` class selector defining the blue background.

```css
.DEG_SEP {
    color:rgb(210, 10, 236);
}

.DT_num {
    color: #b41b1b;
}

.segmentnummer {
    background-color: blue;
}

.segmentkennung {
    background-color: chocolate;
}
```

CSS will allow also overriding attributes so it would as well be possible to
change the separator color. By using rule and token definition names in the html
and class selectors, it’s quite straight-forward and intuitive to adapt the
looks of the html output.

What’s next? Just yesterday I wished I already had that visualizer tool ready
when wading through some segment data in a test case. I’ll tackle decoupling the
input from the source next, that is, parse interactive content.

That’s it for now, thanks for reading!
