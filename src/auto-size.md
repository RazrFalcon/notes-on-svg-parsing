# Automatic document size

A document size is optional in SVG. Meaning you can skip `width`, `height`
and `viewBox` attributes on the root `svg` element.

```xml
<svg xmlns="http://www.w3.org/2000/svg">
    <rect width="100" height="100" fill="green"/>
</svg>
```

The SVG above is perfectly valid. Its size would be resolved to 100x100.

And while the SVG spec does [define](https://www.w3.org/TR/SVG2/coords.html#BoundingBoxes)
the bounding box calculation algorithm, it seems like no one follows it.
Every implementation I've tried uses just the _object bounding box_,
which is not what a user would expect.

First, what about stroking? If our rectangle above had a very thick stroke, should it
be included in the bounding box? According to the spec - yes. In reality - no one does.

What about the filter region? Also not. Your drop shadow would be cut.

What about an image that is actually an SVG as well?
Does it mean we have to parse it as well?
What if that SVG doesn't have size either?

What if our image is just an URL and we have to download it first?

And what about text? Not only its bounding box is not what you might expect
(see the [Text bounding box](./text/bbox.md) chapter).
But to get it in the first place we have to perform
the SVG Text layout algorithm. Which includes system fonts loading,
shaping and text layout (like text-on-path).<br>
Not something you would want to do during parsing.
Meaning that one would have to perform text layout before resolving
the document size, which is far from ideal.

<br>

In the end, we have a rarely used, barely documented feature that even when implemented
"correctly" would produce unexpected results.
