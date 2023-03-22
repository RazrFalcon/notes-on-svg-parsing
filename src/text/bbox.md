# Bounding box

SVG has a concept of coordinates that are relative to object's bounding box.
This is used by gradients, patterns, clip paths, masks and filters.
And it can be useful when we don't know the exact bounding box beforehand.
For example, a text with a random font.<br>
Filters even have a default region that is 10% bigger (in all directions)
than the object's bounding box. Which is handy for something like a blur filter.

For paths and images, a bounding box is pretty straightforward
(note that the path's bounding box doesn't include stroke and markers). But what about text?

Let's say we have something as simple as:

```xml
<text x="100" y="60" font-family="Noto Sans" font-size="48" text-anchor="middle">Text</text>
```

<p align="center">
<img src="images/text-bbox-base.png" width="200"/>
</p>

What do you think is the bounding box of this text?
Something like this (highlighted with a green rectangle)?

<p align="center">
<img src="images/text-bbox-wrong.png" width="200"/>
</p>

Nope! This is a _path_ bounding box, not _text_ bounding box.

To visualize the actual object's bounding box we can use a filter with relative coordinates,
like so:

```xml
<filter id="filter1" x="0%" y="0%" width="100%" height="100%">
    <feFlood flood-color="aquamarine"/>
</filter>
```

This filter will fill a region that equals the object's bounding box with an aquamarine color.
And after applying it to our text we would get:

<p align="center">
<img src="images/text-bbox-actual.png" width="200"/>
</p>

If you're familiar with a modern text layout you're probably not that surprised.
But if you're not, here is a cool illustration from Apple's docs:

<!-- src: https://developer.apple.com/library/archive/documentation/TextFonts/Conceptual/CocoaTextArchitecture/Art/glyph_metrics_2x.png -->
<p align="center">
<img src="images/glyph-metrics.png" height="300"/>
</p>

What we're interested in here is _line height_ (the first vertical measure on the left).
Text layout width + _line height_ is our _text_ bounding box.
This is what should be used when `objectBoundingBox` property is defined.

And while Chrome, Firefox and Safari are all fine,
Batik, Inkscape and librsvg will produce an incorrect output.

## Leading and trailing spaces

By default, all leading and trailing spaces would be removed from a text element in SVG.
Unless we set `xml:space` to `preserve`.
And the spaces we have preserved do affect the bounding box as well.

For an SVG like:

```xml
<text x="100" y="60" text-anchor="middle" xml:space="preserve">  Text  </text>
```

We would get:

<p align="center">
<img src="images/text-bbox-space-preserve.png" width="200"/>
</p>

Chrome, Firefox, Safari and Inkscape handle this correctly, but Batik and librsvg don't.
What's even stranger is that Inkscape accounts for leading/trailing spaces,
but not not line height, which we discussed above.

## Zalgo

Previously, I've said that font's _line height_ is our bounding box height.
Well, this is only true for simple cases.

In a modern text layout a line doesn't technically have a height. Or rather a height limit.
This may sound a bit surprising, but it's true. And a good illustration of this
is [Zalgo text](https://en.wikipedia.org/wiki/Zalgo_text).

Let's try rendering Te̶̗̯̪̺̋̓̍̾́xt using various SVG libraries:

<p align="center">
<img src="images/text-bbox-zalgo.png"/>
</p>

Ugh... Welcome to the hell the SVG Text is.
