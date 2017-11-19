---
layout: post
title:  Markdown mathematical symbol rendering
date:   2017-11-19
categories: General, Mathematics
math: true
---


I've started reading through a book on [Linear Algebra](https://books.google.co.uk/books/about/Linear_Algebra_Concepts_and_Methods.html) and as such I was interested to see what support there was for rendering mathematical symbols on GitHub Pages.  It seems that the syntax below is supported but the setup needed for this site was a little unclear from the documentation

```
$$ a^2 + b^2 = c^2 $$
```

should render as 

$$ a^2 + b^2 = c^2 $$

(If there is nothing rendered prior to the code block above then the rendering setup hasn’t worked which was true in many iterations of this post!  Should be working now)

These were the following changes that enabled the correct rendering on the minima theme

On the posts.html in the _layout directory (this this didn’t exist prior then copy from the minima theme) we’ve added the following prior to the ```<article>``` tag.

```
{% if page.math != false %}
 {% include mathjax.html %}
{% endif %}
```

Now that is in place then (as done on this post) you can include ```math: true``` in the Front Matter.

Finally we now need to actually create the ```mathjax.html``` file in the _includes directory.  For an example of what this file should contain please see [this file](https://github.com/harbour22/harbour22.github.io/blob/master/_includes/mathjax.html)

And if all goes well then the following matrix should render correctly!

$$
A_{m,n} = 
 \begin{pmatrix}
  a_{1,1} & a_{1,2} & \cdots & a_{1,n} \\
  a_{2,1} & a_{2,2} & \cdots & a_{2,n} \\
  \vdots  & \vdots  & \ddots & \vdots  \\
  a_{m,1} & a_{m,2} & \cdots & a_{m,n} 
 \end{pmatrix}
$$


