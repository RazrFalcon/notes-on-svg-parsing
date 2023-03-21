# The `use` element

The `use` element provides a nice feature: instead of copying the same
element multiple times, we can define it once and simply reference it when needed.
Meaning that instead of:

```xml
<g>
    <!-- same elements -->
</g>
<g>
    <!-- same elements -->
</g>
<g>
    <!-- same elements -->
</g>
```

we can write

```xml
<g id="my-group">
    <!-- same elements -->
</g>
<use xlink:href="#my-group"/>
<use xlink:href="#my-group"/>
```

A cool idea that quickly becomes a nightmare.

<br>

The first issue with `use` is style resolving. Let's say we have:

```xml
<g fill="red">
    <rect id="my-rect"/>
</g>
<use xlink:href="#my-rect" fill="green"/>
```

What color the rectangle would have? As you've probably guessed - it's green.<br>
But why? Our rectangle doesn't define a `fill` attribute, therefore we have to look
for it in parent elements. And `my-rect` parent is `g`. So it should be red, no?

No. A referenced element parent is `use`. Not the actual parent in an XML document.
An SVG library should simply replace each `use` instance
with a group, copying all non-`use` attributes into it and
copying the referenced element inside it (ignoring the ID to prevent duplication).
So our example above would become:

```xml
<g fill="red">
    <rect id="my-rect"/>
</g>
<g fill="green">
    <rect/>
</g>
```

<br>

But that's no all. CSS has to be applied to the original tree,
not the one where `use` was already instanced.
