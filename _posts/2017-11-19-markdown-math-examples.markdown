---
layout: post
title:  Markdown math examples
date:   2017-11-19
categories: General
math: true
---

I was curious to see what support there was for mathematical symbols on GitHub Pages.  It seems that the following syntax is supported but a little unclear from the documentation

$$ a^2 + b^2 = c^2 $$

```
$$ a^2 + b^2 = c^2 $$
```

If there is nothing rendered prior to the code block above then the rendering setup hasn’t worked..

So assuming it has worked these were the following changes that enabled this (we’re using the minima theme)

On the posts.html in the _layout directory (this this didn’t exist prior then copy from the minima theme) we’ve added the following prior to the ```<article>``` tag.

```
{% if page.math != false %}
 {% include mathjax.html %}
{% endif %}
```

Now that is in place then (as done on this post) you can include ```math: true``` in the Front Matter.

Finally we now need to actually create the ```mathjax.html``` file in the _includes directory.  For an example of what this file should contain please see [this file](harbour22.github.io/_includes/mathjax.html)

