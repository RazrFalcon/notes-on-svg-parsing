# `letter-spacing`

SVG supports the [letter-spacing](https://www.w3.org/TR/css-text-3/#letter-spacing-property)
property from CSS. On the surface, it's a pretty simple feature.
Just add spacing between letters. How hard can it be?!
Well, everything that involves text is hard.

The first issue with `letter-spacing` is that not all scripts (language categories)
support spacing. The best example is Arabic, aka a cursive script.
CSS even has [a good illustration](https://www.w3.org/TR/css-text-3/#cursive-tracking) of it.
But the problem is that it seems like no one cares. At least for SVG.<br>
Instead of نص we're getting:

<img src="images/letter-spacing-arabic.png">

Safari, somehow, even disabled shaping completely.

Another issue is that the CSS spec explicitly states that spacing should be
applied only _between_ letters. So there should be no leading/trailing spaces.
But this one is ignored by browsers as well.<br>
This is how the following SVG would be rendered:

```xml
<text x="100" text-anchor="middle" letter-spacing="100" font-size="48">A</text>
```

<img src="images/letter-spacing-tail.png">

As you can see, Chrome, Firefox and Batik are clearly adding a trailing space.
That's why center alignment is broken.

Negative spacing is basically an undefined behavior as well. And while a small negative value
should put letters close to each other, a larger one produces random results.

```xml
<text x="100" y="100" letter-spacing="-1000" text-anchor="middle">Text</text>
```

<img src="images/letter-spacing-negative.png">
