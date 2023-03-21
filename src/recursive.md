# Recursive references

SVG provides two types of references: IRI and FuncIRI.<br>
IRI is defined via `xlink:href="#id"` and used by `use` elements,
gradients, patterns and filters.<br>
FuncIRI is defined via `url(#id)` and used by `fill`, `stroke`, `clip-path`, `mask`, `filter`
and many more attributes.

The main downside of any kind of references/linking system is recursive references.
And while most SVG quirks would at worse lead to an incorrect rendering,
this one can easily crash your application. And often does.

And the wast complexity of SVG only makes it worse.
Not only there are many ways a recursive reference can be triggered,
but some cases are also hard to detect.
Below are some examples.

<br>

Self-recursive:

```xml
<use id="use1" xlink:href="#use1"/>
```

<br>

Simple recursive:

```xml
<use id="use1" xlink:href="#use2"/>
<use id="use2" xlink:href="#use1"/>
```

<br>

Indirect recursive:

```xml
<g id="g1">
    <use xlink:href="#use2"/>
</g>
<use id="use2" xlink:href="#g1"/>
```

```xml
<!-- this one affects masks and patterns as well -->
<clipPath id="clip1">
    <rect clip-path="url(#clip2)"/>
</clipPath>
<clipPath id="clip2">
    <rect clip-path="url(#clip1)"/>
</clipPath>
```

<br>

Remember that XML entities can also be recursive
(see [billion laughs attack](https://en.wikipedia.org/wiki/Billion_laughs_attack)).
But it's technically an XML parser problem and not SVG one.
