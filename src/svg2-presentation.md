# Presentation attributes in SVG 2

SVG has a concept of [presentation attributes](https://www.w3.org/TR/SVG11/attindex.html#PresentationAttributes).
Essentially, those are style properties, like `fill`, that can be set via
CSS, a `style` attribute and as a regular XML attribute.
Meaning that all of the following examples are functionally identical:

```xml
<style>
    #rect1 { fill:green }
</style>
<rect id="rect1"/>
```

```xml
<rect style="fill:green"/>
```

```xml
<rect fill="green"/>
```

SVG 2 changed this behavior and all new presentation attributes are no longer allowed to be used
as regular XML attributes. So you can write `style="mix-blend-mode:screen"`,
but not `mix-blend-mode="screen"`.

I have no idea why it was done, but it's a really annoying change.
All SVG 2 presentation attributes had to be skipped during XML attributes parsing now.
And an SVG writing library is essentially forced to use `style` everywhere,
which makes a resulting SVG harder to read and parse.
