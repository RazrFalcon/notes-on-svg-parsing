# `use` to `svg` size resolving

The `use` element can reference almost all graphical elements, including `svg`.
But this case has to be handled differently.

If you read [the spec](https://www.w3.org/TR/SVG11/struct.html#UseElement) very carefully,
you will notice the following line:

>If attributes `width` and/or `height` are provided on the `use` element, then these values
will override the corresponding attributes on the `svg` in the generated tree.

This means that in the case of:

```xml
<svg id="svg1" width="80" height="80">
    <rect/>
</svg>
<use xlink:href="#svg1" width="100" height="150"/>
```

The `width` and `height` properties on the `svg1` element must be ignored and replaced
with the one provided by `use`.

Weird, but not that strange for SVG. The next problem lies in the part not described in the spec.
What if we have something like this:

```xml
<svg id="svg1" width="80" height="80">
    <rect/>
</svg>
<use id="use1" xlink:href="#svg1" height="100"/>
<use id="use2" xlink:href="#use1" width="200"/>
```

Should we ignore the `width` from `use2`? Yes we do!<br>
`width` and/or `height` only from the direct "parent" `use` element must be used.
So our `svg1` would be 80x100 and not 200x100.
