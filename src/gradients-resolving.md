# Gradients resolving

A typical gradient in SVG looks like this:

```xml
<linearGradient id="lg1">
    <stop offset="0" stop-color="white"/>
    <stop offset="1" stop-color="black"/>
</linearGradient>
```

But SVG allows one gradient to reference another via the `href` attribute.
When used, gradient's missing stop elements and attributes will be copied from a referenced gradient.

Most libraries are either bad at this or do not support this at all.

## Stops resolving

A gradient can reference stop elements from another gradient.
The type of a gradient doesn't matter, meaning linear can reference radial and vise versa.
Stops will be referenced only when the current gradient doesn't have any stops.
Meaning that

```xml
<linearGradient id="lg1">
    <stop offset="0" stop-color="white"/>
    <stop offset="1" stop-color="black"/>
</linearGradient>
<linearGradient id="lg2" xlink:href="#lg1"/>
```

is identical to

```xml
<linearGradient id="lg1">
    <stop offset="0" stop-color="white"/>
    <stop offset="1" stop-color="black"/>
</linearGradient>
<linearGradient id="lg2">
    <stop offset="0" stop-color="white"/>
    <stop offset="1" stop-color="black"/>
</linearGradient>
```

The `href` chain length is unlimited. A gradient can reference another
gradient which references another one and so on until one of the gradients in a chain
has non-zero stops.
Like so:

```xml
<linearGradient id="lg1">
    <stop offset="0" stop-color="red"/>
    <stop offset="1" stop-color="green"/>
</linearGradient>
<!-- lg1 stops are ignored, because lg2 already has stops -->
<linearGradient id="lg2" xlink:href="#lg1">
    <stop offset="0" stop-color="white"/>
    <stop offset="1" stop-color="black"/>
</linearGradient>
<linearGradient id="lg3" xlink:href="#lg2"/> <!-- stops from lg2 and not lg1 will be used -->
<linearGradient id="lg4" xlink:href="#lg3"/> <!-- stops from lg2 via lg3 will be used  -->
```

## Attributes resolving

Similar rules apply to gradient attributes as well.
If a gradient has a `href` link to another gradient and the referenced gradient
has attributes not present in the current gradient - those attributes will be copied.

But things are getting weird pretty fast. Some attributes can be copied only from
a gradient of the same type. Specifically `x1`, `y1`, `x2` and `y2` _linear_ gradient attributes
can be copied only from other _linear_ gradients. Even when one of gradients in the `href`
chain is radial. And `cx`, `cy`, `fx`, `fy` and `r` _radial_ gradient attributes
can be copied only from other _radial_ gradients. <br>
But `gradientUnits`, `spreadMethod` and `gradientTransform` attributes
can be copied from a gradient of any type.

In this example, `lg1` will copy `spreadMethod` from `rg1`, but will ignore `y2`.

```xml
<radialGradient id="rg1" y2="1" spreadMethod="reflect"/>
<linearGradient id="lg1" xlink:href="#rg1" x2="0.7">
    <stop offset="0" stop-color="white"/>
    <stop offset="1" stop-color="black"/>
</linearGradient>
```

<br>

By extrapolating a bit, we can easily end up with something like this:

```xml
<radialGradient id="rg1" xlink:href="#rg2" cx="0.5"/>
<radialGradient id="rg2" xlink:href="#rg3" spreadMethod="reflect" fy="0.65"/>
<radialGradient id="rg3" cy="0.55"/>
<radialGradient id="rg4" xlink:href="#rg1" r="0.5" fx="0.6"/>
<rect fill="url(#rg4)"/>
```

which is identical to:

```xml
<radialGradient id="rg4" cx="0.5" cy="0.55" fx="0.6" fy="0.65" r="0.5" spreadMethod="reflect"/>
<rect fill="url(#rg4)"/>
```

## Radial gradient attributes resolving

Radial gradients increase the complexity even more by requiring a specific order in which attributes
must be resolved.
Specifically, `fx` and `fy` attributes, when not set, are equal to `cx` and `cy`.
But only when a referenced gradient doesn't set `fx` and `fy` either.
So in the case of:

```xml
<radialGradient id="rg1" cy="0.7" fx="0.8"/>
<radialGradient id="rg2" xlink:href="#rg1" cx="0.4" r="0.1"/>
```

`fx` will be resolved to `0.8` and not `0.4`, because it's present in a referenced element.<br>
And `fy` will be resolved to `0.7` and not `0.5`, which is the default `cx`/`cy` value.

Basically, `rg2` first becomes:

```xml
<radialGradient id="rg2" cx="0.4" cy="0.7" fx="0.8" r="0.1"/>
```

And only then we resolve missing `fx`/`fy` attributes and end up with:

```xml
<radialGradient id="rg2" cx="0.4" cy="0.7" fx="0.8" fy="0.7" r="0.1"/>
```

<br>

Note that stops and attributes resolving are independent.

## Patterns and filters

Patterns have a similar logic for resolving its attributes and children.
And filter has a similar logic for resolving its attributes, but not children.
