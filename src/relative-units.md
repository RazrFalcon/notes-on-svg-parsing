# Relative units

Just like HTML/CSS, SVG supports [relative units](https://www.w3.org/TR/css-values-3/#relative-lengths).
In fact, SVG 2 supports the same units HTML/CSS does.
So the logic can overlap between SVG and HTML.

And while absolute units (like `mm`) are pretty simple - just a multiplication factor based on the
[DPI](https://en.wikipedia.org/wiki/Dots_per_inch),
relative require a much more complex logic.

## Percent units

While percent units may look simple, after all, % can be treated just as a factor
(e.g. 200% is the same as 2x).
But in reality, they are quite complicated. And mainly because of the base value
they are _relative to_.

Let's take opacity for example: `opacity="50%"`. What is it relative to? Technically, to itself.
Or more specifically to 1. So 50% equals 0.5 and 100% equals 1.
And values outside the 0..1 range are clamped.

What about the rectangle's width? It's relative to the parent SVG element's `viewBox`
(_parent_, not the _root_ one, and `viewBox`, not `width`/`height`). So in the case of:

```xml
<svg width="100" viewBox="0 0 50 50">
    <rect width="50%" height="20"/>
</svg>
```

our `width` is 25.

And it gets weirder since a `use` element can enforce the `svg` viewbox as well when no
`viewBox` was set. So in the case of:

```xml
<svg id="svg1" width="100">
    <rect width="50%" height="20"/>
</svg>
<use xlink:href="#svg1" width="50">
```

our `width` is again 25. Because we have to use the width from the `use` element.
You can find more about it in the
[`use` to `svg` size resolving](./use/use-to-svg-size-resolving.md) chapter.

<br>

What about `stroke-width`? Well, it's relative to the _diagonal length_ of the current viewbox.
Aka `sqrt((width^2 + height^2) / 2)`. So in the case of:

```xml
<svg viewBox="0 0 50 25">
    <rect stroke-width="50%"/>
</svg>
```

it will be `sqrt((50^2 + 25^2) / 2) = 39.5`.

<br>

What about `font-size`? It's relative to the parent `font-size`. So in the case of:

```xml
<g font-size="20">
    <text font-size="50%"/>
</g>
```

our font size is 10.

## Font units

`font-size` and font units (`em` and `ex`) make our lives even worse.

First, `font-size` can have
[named values](https://www.w3.org/TR/2008/REC-CSS2-20080411/fonts.html#propdef-font-size),
like `larger` or `medium`.
It can also have percent units, which are relative to the parent `font-size`
and not the current viewbox.

Second, font relative units have to be resolved from the root element to the current one
and not the other way around, like with other relative units. So in the case of:

```xml
<svg>
    <g font-size="20">
        <g font-size="200%">
            <g font-size="larger">
                <rect width="0.5em" height="0.5ex"/>
            </g>
        </g>
    </g>
</svg>
```

our width and height are 24 and 12 respectively. Why? Because of the following steps
(from top to bottom):

- 20
- 20 * 200% = 40
- 40 * 1.2 = 48 (`larger` has a scaling factor of 1.2, don't ask why...)
- width: 48 * 0.5 = 24
- height: (48 * 0.5) / 2 = 12
