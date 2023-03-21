# `rx`/`ry` attributes resolving

In SVG, rectangle corners can be rounded by setting `rx` and `ry` attributes. Like this:

```xml
<rect x="5" y="5" width="10" height="10" rx="2" ry="2"/>
```

duplicated radiuses. So instead of `rx="2" ry="2"` you can write just `rx="2"` or `ry="2"`.
And the "missing" value will be copied from the present one. Pretty nice.

Of course, it wouldn't be in this "book" if it was that easy.
Remember that SVG supports [relative units](./relative-units.md),
like `mm`, `em` and `%`, which you have to convert into absolute before rendering?
The question is, what should be done first: resolving the missing attribute
or relative to absolute unit conversion? The spec doesn't say.

But who cares! It couldn't be that bad, right? Well... Let's take a look at this example:

```xml
<svg viewBox="0 0 20 50">
    <rect width="10" height="10" rx="50%"/>
</svg>
```

To what values `rx` and `ry` should be resolved?
If we copy the missing radius first, then we will get `rx="50%" ry="50%"`
and therefore `rx="10" ry="25"` (via `svg.viewBox.width * 50%` and `svg.viewBox.height * 50%`).
Otherwise it will be `rx="10" ry="10"`.

The answer is the latter.
A parser is suppose to convert units first and only then copy the present value.

Neat! But surely no one actually made such mistake, right?
Well, Batik 1.16, a 20 years old library, still has this bug.
My `resvg` library had this bug for 5 years until I accidentally stumbled onto it while fixing
an unrelated issue.

A tiny bug that is almost impossible to hit, but that's what you get when you have a file format
with countless interconnected features and no test suite.

<br>

Since SVG 2, this logic affects `ellipse` as well.
