# Whitespaces handling

Where do I even begin with this one... I think overall, I spent around a month
just to figure out how to properly extract text with whitespaces from an SVG.
Not only the way SVG treats whitespaces is utterly unintuitive, but after you add `xml:space`
to the mix, it becomes a truly horrifying thing.
To this day, most SVG libraries do not handle `xml:space` properly.
And to make things worse, SVG 2 added the `white-space` property, which is incompatible with `xml:space`.

Note that while the root of the problem starts with XML, it's only partially responsible to this mess.

Whitespaces handling is roughly split into two steps: XML side and SVG side.
The only thing a proper XML library should do is to replace new lines with spaces.
It must not trim them and/or post-process them in any other way.
All of this should be done by an SVG library.

Let's say we have a text like this:

```xml
<text> A   lot     of
spaces</text>
```

it should be parsed as `A lot of spaces`. No leading, trailing and duplicated whitepaces.
Easy so far.

If we add `xml:space` to the mix:

```xml
<text xml:space="preserve"> A   lot     of
spaces</text>
```

then it would be parsed as `␣A␣␣␣lot␣␣␣␣␣of␣spaces`. New lines were replaced with spaces,
but that's about it.

How about we add some style to it:

```xml
<text>
    <tspan>
        Some
    </tspan>
    <tspan>
        text
    </tspan>
</text>
```

This one should be parsed as `Some text`. Why? Because if we trim white spaces we would end up with:

```xml
<text><tspan>Some</tspan> <tspan>text</tspan></text>
```

Yes, the space between non-empty `tspan`s should be preserved.
Inkscape 1.2 fails to handle this correctly.

Those two should be handled the same way:

```xml
<text>
    <tspan>
        Text
    </tspan>
    Text
</text>
```

```xml
<text>
    <tspan>
        Text
        <tspan>
            Text
        </tspan>
    </tspan>
</text>
```

And this time Inkscape manages to parse them correctly.

<br>

Let's look into something more interesting, like:

```xml
<text>
    Text  <tspan xml:space="preserve">  Text  </tspan>  Text
</text>
```

Which should be parsed as `Text␣␣␣Text␣␣Text`. Yes, 3 and 2 spaces respectively.
Why? I don't know. Apparently this obvious case isn't well defined in the SVG/XML spec.
Chrome, Batik and resvg will produce 3 and 2 spaces, while Firefox, Safari, Inkscape, librsvg and QtSvg
will produce 3 and 3 spaces.

Note that `resvg` simply follows our overlord Chrome here,
because this is the output most people would expect.
This is not about being right or wrong, but about the market share.

<br>

Similarly, an inverted version of the text above:

```xml
<text xml:space="preserve">  Text  <tspan xml:space="default">  Text  </tspan>  Text  </text>
```

should be parsed as `␣␣Text␣␣Text␣␣␣Text␣␣`.

<br>

But we're not done yet! Let's try making it even worse:

```xml
<text xml:space="preserve">
    <tspan xml:space="default">
        Some
        <tspan xml:space="preserve">
            text
        </tspan>
    </tspan>
</text>
```

Everyone except Firefox agrees that it should be parsed as
`␣␣␣␣␣Some␣␣␣␣␣␣␣␣␣␣␣␣␣␣text␣␣␣␣␣␣␣␣␣␣`.
So 5, 14 and 10. Or:

```xml
<text xml:space="preserve">     <tspan xml:space="default">Some <tspan xml:space="preserve">             text         </tspan></tspan> </text>
```

Firefox will ignore the first 5 whitespaces for some reason. Probably a bug.

<br>

Just think about it for a second. We haven't talked about fonts, styles, decorations, or alignment.
No. This is just whitespaces. The first text parsing step. And we already cannot have reproducible results.
