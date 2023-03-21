# Attributes inheritance

SVG is all about simplifying the life of an author who writes SVG by hand.
One of such features is attributes inheritance.

Instead of writing:

```xml
<rect fill="green"/>
<rect fill="green"/>
```

we can write

```xml
<g fill="green">
    <rect/>
    <rect/>
</g>
```

and both rectangles will _inherit_ the `fill` attribute value.

As expected, this simple idea leads to death by a thousand cuts.

First, not all properties/attributes are inheritable.
For example, `fill-opacity` is inheritable, but `opacity` isn't.
You can find the whole list [here](https://www.w3.org/TR/SVG11/propidx.html)
(see the _Inh._ column).

Second, for some reason, inheritance can be stated explicitly, like this:

```xml
<g fill="green">
    <rect fill="inherit"/>
    <rect/>
</g>
```

This would produce the same output as before, but makes parsing harder.

Third, some non-inheritable properties can be inherited explicitly
via the `inherit` keyword, but only from the direct parent element.<br>
For example:

```xml
<g opacity="0.5">
    <rect/> <!-- opacity=1 -->
    <rect opacity='inherit'/> <!-- opacity=0.5, effectively 0.25 -->
    <g> <!-- opacity=1 -->
        <rect opacity='inherit'/> <!-- opacity=1, effectively 0.5 -->
    </g>
    <g opacity='inherit'> <!-- opacity=0.5, effectively 0.25 -->
        <rect opacity='inherit'/> <!-- opacity=0.5, effectively 0.125 -->
    </g>
</g>
```

<br>

Fourth, when the `inherit` keyword is used, but no parent element had defined this property,
the default value should be used.

<br>

All of this can lead to some bizarre and unexpected cases like:

```xml
<g stop-color="red">
    <linearGradient id="lg1" stop-color="green">
        <stop offset="0"/>
        <stop offset="1" stop-color="inherit"/>
    </linearGradient>
</g>
```

Here, the first stop would be black (default value, because `stop-color` is non-inheritable)
and the second stop would be green (explicitly inherited from the parent).
And the `stop-color` on `g` has no effect, because it's non-inheritable.
