# Stroke paint fallback

Let's say we want to stroke a shape with a gradient or a pattern.
To do so we should use a familiar construct like `stroke="url(#gradient)"`.
Pretty easy!

But remember that our gradient or pattern can have `objectBoundingBox` units.
In which case their size is relative to the shape's bounding box. <br>
And what if our shape has zero width or height? This is not an error.
After all, a horizontal line has no height and a vertical line has no width.
But out gradient's or pattern's bounding box will be zero then. Not good.

The SVG spec thought about that by making everything more complicated than it should be.
An obvious solution would be to simply skip such shapes.
Which is indeed the default behavior. <br>
Instead, SVG provides an additional syntax `stroke="<FuncIRI> <color>"` just for this case.
Meaning we can write `stroke="url(#gradient) green"` and when a shape has zero
width or height, it will be stroked using the green color and not simply ignored.

When it can be useful? No idea.<br>
And as expected, this trips some libraries. Mainly Inkscape and QtSvg.

<br>

Note that this feature affects only stroking, since zero width/height fill will always
produce nothing.
